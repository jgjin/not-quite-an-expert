---
title: "Let's actually build a random Spotify album selector!"
date: 2020-05-10
draft: false
tags: ["programming", "technology", "art", "music"]
---
# Introduction
Okay, for real this time. I'm going to do it in Rust because I like Rust. [Code is available in a GitHub repo for reference](https://github.com/jgjin/random_album), and I'll be linking files where I can.

We're going to cover a lot of technologies (including a pretty large tour of Rust), so get ready!
# Installing Rust
Installing Rust is easy (on non-Windows systems). In terminal, run:
```zsh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
Great! You did it!
# Cargo
Cargo is the package manager for Rust. We also manage projects through Cargo. In terminal, run:
```zsh
cargo new random_album
cd random_album
```
We can see Cargo generated a [Cargo.toml](https://github.com/jgjin/random_album/blob/master/Cargo.toml) file. We declare configuration and dependencies there.

Now that we've created our project, let's outline what we want this project to do:
1. Retrieve albums in the user's Spotify library
2. Randomly select one and return it to the user

That's really it! Of course, the devil is in the details.
# Rocket
Rocket is a web framework for Rust. We can add it as a dependency by putting it in the `[dependencies]` section of [Cargo.toml](https://github.com/jgjin/random_album/blob/master/Cargo.toml):
```toml
[dependencies]
rocket = "0.4"
```
In general, the format of declaring dependencies is crate name = version. "Crate" is Rust slang for packages. 

Now in [src/main.rs](https://github.com/jgjin/random_album/blob/master/src/main.rs):
```Rust
#![feature(proc_macro_hygiene, decl_macro)]

#[macro_use]
extern crate rocket;

#[get("/")]
fn index() -> &'static str {
    "Hello, world!"
}

fn main() {
    rocket::ignite().mount("/", routes![index]).launch();
}
```

Now we can compile and run our project with `cargo run` (assuming we're still in the `random_album` directory). We now have a working web application!
# OAuth
OAuth is the go-to standard for allowing third-party applications to access user data. The basic process of OAuth is as follows (for convenience, let's call our application Album Selector):
1. Album Selector registers as a third-party application to Spotify
2. The user wants to use Album Selector
3. Album Selector redirects the user to a Spotify link
4. The user tells Spotify to grant user data permissions to Album Selector
5. Spotify gives Album Selector a token with user data permissions for that user
6. Album Selector uses that token in all future calls to access that user's data
## Spotify OAuth
We can register our application through [the Spotify developer dashboard](https://developer.spotify.com/dashboard/). We're going to follow [the authorization code flow](
https://developer.spotify.com/documentation/general/guides/authorization-guide/#authorization-code-flow). It'll be helpful to keep the authorization code flow guide open as reference as we implement OAuth.
## Rust code
Now the OAuth code. First, we need to bring in some new dependencies. In our [Cargo.toml](https://github.com/jgjin/random_album/blob/master/Cargo.toml):
```toml
[dependencies]
chashmap = "2.2"
chrono = "0.4"
dotenv = "0.15"
oauth2 = "3.0.0-alpha.9"
rand = "0.7"
reqwest = { version = "0.10.4", features = ["blocking", "json"] }
rocket = "0.4"
serde = { version = "1.0", features = ["derive"] }
ttl_cache = "0.5"
```
`rocket` is from before, and the rest of the crates we'll talk about when we get to them.
### Rocket state
In order to support multiple users, our application will need to manage state. That is, it will need to associate each user's OAuth token with that user's temporary username. Since HTTP is asynchronous, this state must support concurrency, so we use `chashmap`. Fortunately, Rocket supports state in a pretty straightforward manner. Change [src/main.rs](https://github.com/jgjin/random_album/blob/master/src/main.rs) to:
```Rust
#![feature(proc_macro_hygiene, decl_macro)]

extern crate chashmap;
extern crate chrono;
extern crate dotenv;
extern crate oauth2;
extern crate rand;
extern crate reqwest;
#[macro_use]
extern crate rocket;
extern crate serde;
extern crate ttl_cache;

use chashmap::CHashMap;
use dotenv::dotenv;

mod assets;
mod cache;
mod error;
mod oauth;
mod oauth_token;
mod random_album;
mod types;

fn main() {
    dotenv().ok();

    rocket::ignite()
        .manage(CHashMap::<String, oauth::UserState>::new())
        .manage(cache::Cache::new())
        .mount(
            "/",
            routes![
                assets::favicon,
                error::error,
                oauth::begin_oauth,
                oauth::end_oauth_accept,
                oauth::end_oauth_deny,
                random_album::next_random_album,
            ],
        )
        .launch();
}
```
We've also made some changes to endpoints and environment variables, which we'll progressively describe next.
### OAuth logic
Let's write the OAuth logic in a separate file [src/oauth.rs](https://github.com/jgjin/random_album/blob/master/src/oauth.rs). Here's the code broken down in order by sections:
#### Imports
```Rust
use chashmap::CHashMap;
use oauth2::basic::{BasicClient, BasicTokenType};
use oauth2::reqwest::http_client;
use oauth2::{
    AuthUrl, AuthorizationCode, ClientId, ClientSecret, CsrfToken, EmptyExtraTokenFields,
    RedirectUrl, Scope, StandardTokenResponse, TokenUrl,
};
use rocket::http::{Cookie, Cookies, RawStr, SameSite};
use rocket::response::Redirect;
use rocket::State;

use std::convert::TryFrom;
use std::env;

use crate::oauth_token::OAuthToken;
```
`use` statements act like `import` statements in Python. They import packages, allowing you to reference them by shorter names.
#### Type declarations
```Rust
pub type TokenResponse = StandardTokenResponse<EmptyExtraTokenFields, BasicTokenType>;
```
This is a type alias for convenience.
```Rust
pub enum UserState {
    NeedsUserAuth(BasicClient),
    FinishedAuth(OAuthToken),
}
```
Rust `enum`s are quite rich; they can encapsulate different types depending on the enum value. Here, the `UserState` is either `NeedsUserAuth`, which holds an OAuth client, or `FinishedAuth`, which holds an OAuth token.
#### `begin_oauth`
```Rust
#[get("/")]
pub fn begin_oauth(oauth_tokens: State<CHashMap<String, UserState>>) -> Redirect {
    let client = create_client();

    let (auth_url, temp_username) = client
        .authorize_url(CsrfToken::new_random)
        .add_scope(Scope::new("user-library-read".to_string()))
        .url();

    oauth_tokens.insert(
        temp_username.secret().to_string(),
        UserState::NeedsUserAuth(client),
    );

    return Redirect::found(auth_url.into_string());
}
```
Note: make sure to update your application's whitelisted redirect URIs in the [the Spotify developer dashboard](https://developer.spotify.com/dashboard/) if you're following along. For local testing, you'll probably want to set it to `http://localhost:8000/oauth_callback`.

`begin_oauth` serves our `/` endpoint. It creates an OAuth client with the appropriate configuration via `create_client`, which uses the `oauth2` crate. Next, it generates a securely random [CSRF token](https://portswigger.net/web-security/csrf/tokens) as a temporary username. It then tells Rocket to associate the created OAuth client with the temporary username (in `State<CHashMap<String, UserState>>`) for later use. Finally, it redirects the user to a Spotify link to ask user data permissions.
#### `end_oauth_accept`
```Rust
#[get("/oauth_callback?<code>&<state>")]
pub fn end_oauth_accept(
    code: &RawStr,
    state: &RawStr,
    oauth_tokens: State<CHashMap<String, UserState>>,
    mut cookies: Cookies,
) -> Redirect {
    let temp_username = state.url_decode_lossy();

    let redirect_url = oauth_tokens
        .get_mut(&temp_username)
        .map(|mut user_state| match &*user_state {
            UserState::NeedsUserAuth(client) => client
                .exchange_code(AuthorizationCode::new(code.url_decode_lossy()))
                .request(http_client)
                .ok()
                .and_then(|token_response| {
                    OAuthToken::try_from(token_response)
                        .ok()
                        .map(|oauth_token| {
                            *user_state = UserState::FinishedAuth(oauth_token);

                            let mut cookie = Cookie::new("temp_username", temp_username.clone());
                            cookie.set_same_site(SameSite::Lax);
                            cookies.add_private(cookie);

                            "/random_album"
                        })
                })
                .unwrap_or_else(|| {
                    oauth_tokens.remove(&temp_username);

                    "/error"
                }),
            _ => "/error",
        })
        .unwrap_or("/error");

    return Redirect::found(redirect_url);
}
```
If the user grants permission, Spotify calls the `/oauth_callback` endpoint with the query parameters `code` and `state`. `state` will hold the temporary username from `begin_oauth`. We then exchange `code` for an OAuth token using the OAuth client associated with `state` (i.e. the temporary username) from `begin_oauth`. 

If any step resulted in error, we redirect to `/error`. Otherwise, we redirect the user to `/random_album`.
#### Cookies
In order to identify the user in future requests, we set the private (i.e. secure) "temp_username" cookie. Unfortunately, [cookies can be weird](https://github.com/SergioBenitez/Rocket/issues/1015), so we need to set `SameSite` to `Lax`. If that doesn't mean anything, don't worry. It doesn't really mean anything to me either.
#### `end_oauth_deny`
```Rust
#[get("/oauth_callback?error&<state>")]
pub fn end_oauth_deny(
    state: &RawStr,
    oauth_tokens: State<CHashMap<String, UserState>>,
) -> Redirect {
    let temp_username = state.url_decode_lossy();
    oauth_tokens.remove(&temp_username);

    return Redirect::found("/error");
}
```
If the user denies permission, we forget the temporary username and redirect to `/error`.
# Environment variables
Let's take a look at `create_client`, the function used in `begin_oauth` to create an OAuth client:
```Rust
pub fn create_client() -> BasicClient {
    BasicClient::new(
        ClientId::new(env::var("CLIENT_ID").expect("CLIENT_ID env var")),
        Some(ClientSecret::new(
            env::var("CLIENT_SECRET").expect("CLIENT_SECRET env var"),
        )),
        AuthUrl::new("https://accounts.spotify.com/authorize".to_string()).expect("AuthUrl"),
        Some(
            TokenUrl::new("https://accounts.spotify.com/api/token".to_string()).expect("TokenUrl"),
        ),
    )
    .set_redirect_url(
        RedirectUrl::new(env::var("REDIRECT").expect("REDIRECT env var")).expect("RedirectUrl"),
    )
}
```
What's that `env::var` thing? When building applications, for convenience and security it's often a bad idea to hard-code in security- or environment-sensitive values. In our case, the client ID and client secret are somewhat security-sensitive, so we make them environment variables. The redirect URL is environment-sensitive, so we also make it an environment variable. We use the `dotenv` crate (called in [src/main.rs](https://github.com/jgjin/random_album/blob/master/src/main.rs)), to set these environment variables in a `.env` file:
```zsh
CLIENT_ID="<redacted>"
CLIENT_SECRET="<redacted>"
REDIRECT="http://localhost:8000/oauth_callback"
```
# Errors
Luckily, if Rocket ever encounters an error while handling an endpoint, it will return a 40X or 50X HTTP response status code. If there is some data flow error we'll redirect to `/error`, as you've seen before. In [src/error.rs](https://github.com/jgjin/random_album/blob/master/src/error.rs):
```Rust
#[get("/error")]
pub fn error() -> String {
    "Oh no! An error happened (probably in getting Spotify user data)!".to_string()
}
```
# Structs and traits
You might remember `UserState::FinishedAuth` holds an `OAuthToken`. This is a custom type defined in [src/oauth_token.rs](https://github.com/jgjin/random_album/blob/master/src/oauth_token.rs):
```Rust
use chrono::{DateTime, Utc};
use oauth2::reqwest::http_client;
use oauth2::{RefreshToken, TokenResponse};

use std::convert::TryFrom;

use crate::oauth;

pub struct OAuthToken {
    token: String,
    expiration: DateTime<Utc>,
    refresh_token: String,
}
```
In Rust, there are only `struct`s,[^1] there is no `class` (tehe). `struct`s can implement their own member functions, such as:
[^1]: I put `struct` in a special font to emphasize Rust `struct`s are special. 
```Rust
impl OAuthToken {
    pub fn token_checked(&mut self) -> Result<String, &'static str> {
        if Utc::now() >= self.expiration {
            return self.refresh().map(|_| self.token.clone());
        }

        return Ok(self.token.clone());
    }

    fn refresh(&mut self) -> Result<(), &'static str> {
        let client = oauth::create_client();

        client
            .exchange_refresh_token(&RefreshToken::new(self.refresh_token.clone()))
            .request(http_client)
            .map_err(|_| "Error in refresh token request")
            .and_then(|token_response| {
                Self::try_from(token_response).map(|oauth_token| {
                    self.token = oauth_token.token;
                    self.expiration = oauth_token.expiration;
                    self.refresh_token = oauth_token.refresh_token;
                })
            })
    }
}
```
In fact, you can even extend `struct`s defined elsewhere:
```Rust
impl oauth::UserState {
    pub fn token_checked(&mut self) -> Result<String, &'static str> {
        match self {
            oauth::UserState::FinishedAuth(oauth_token) => oauth_token.token_checked(),
            _ => Err("User has not finished OAuth"),
        }
    }
}
```
Instead of using inheritance from object-oriented design, Rust `struct`s implement `trait`s. Roughly, Rust `trait`s are functions that must be implemented by a `struct`. For example, we can implement trait `TryFrom<oauth::TokenResponse>` for `OAuthToken` to define how to fallibly convert from `oauth::TokenResponse` to `OAuthToken`:
```Rust
impl TryFrom<oauth::TokenResponse> for OAuthToken {
    type Error = &'static str;

    fn try_from(token_response: oauth::TokenResponse) -> Result<Self, Self::Error> {
        Ok(Self {
            token: token_response.access_token().secret().to_string(),
            expiration: Utc::now()
                .checked_add_signed(
                    chrono::Duration::from_std(token_response.expires_in().ok_or(
                        "Spotify authorization code flow should always return expiration period!",
                    )?)
                    .map_err(|_| "Could not interpret expiration period")?,
                )
                .ok_or("Could not interpret expiration time")?,
            refresh_token: token_response
                .refresh_token()
                .ok_or("Spotify authorization code flow should always return refresh token!")?
                .secret()
                .to_string(),
        })
    }
}
```
`trait`s in Rust prevent [multiple inheritance issues](https://en.wikipedia.org/wiki/Multiple_inheritance). In addition, they can make code more readable by directly describing the required properties (i.e. behaviors, i.e. functions) of inputs and outputs.

For the curious, `chrono` is Rust's time and date library.
# Spotify API
## serde
Now we can finally access the user's data (assuming they gave us permissions during OAuth). One of the coolest things about Rust is the `serde` crate. Let's take a peek at [src/types.rs](https://github.com/jgjin/random_album/blob/master/src/types.rs):
```Rust
use serde::Deserialize;

use std::collections::HashMap;

#[derive(Deserialize, Debug)]
pub struct Paging<T> {
    pub items: Vec<T>,
    pub next: Option<String>,
}

#[derive(Clone, Deserialize, Debug)]
pub struct SavedAlbum {
    pub album: Album,
}

#[derive(Clone, Deserialize, Debug)]
pub struct Album {
    pub artists: Vec<Artist>,
    pub images: Vec<Image>,
    pub name: String,
    pub external_urls: HashMap<String, String>,
    pub copyrights: Vec<Copyright>,
}

#[derive(Clone, Deserialize, Debug)]
pub struct Artist {
    pub name: String,
}

#[derive(Clone, Deserialize, Debug)]
pub struct Image {
    pub url: String,
}

#[derive(Clone, Deserialize, Debug)]
pub struct Copyright {
    pub text: String,
    #[serde(rename = "type")]
    pub copyright_type: String,
}
```
These `struct`s are based on [the Spotify API reference](https://developer.spotify.com/documentation/web-api/reference/library/get-users-saved-albums/).

If you recall, `struct`s in Rust implement `trait`s. If the trait can be automatically implemented for a `struct`, you can `derive` the trait by annotating before its declaration, like we did above. `serde` allows us to `derive` a trait called `Deserialize`. By doing so, we can convert from any common data representation, such as JSON, to the `struct`. Seriously! There's no tedious parsing code for our project! `derive`-ing `Deserialize` did it for us! 

`serde` even lets us use field names that are normally reserved keywords in Rust:
```Rust
#[serde(rename = "type")]
pub copyright_type: String,
```

Note: `serde` will ignore JSON fields not specified in our `struct`s.
## Pagination
You'll notice we've defined a `Paging` type. When we deal with collections of arbitrary size, it makes the most sense to divided the items into pages. Each page has some specified number of items, and we can retrieve the entire collection page by page.

In order to handle pagination, our code keeps retrieving the next page until it doesn't exist. In [src/random_album.rs](https://github.com/jgjin/random_album/blob/master/src/random_album.rs):
```Rust
fn next_album(
    username: &str,
    album_cache: &State<Cache>,
    user_state: &mut UserState,
) -> Option<SavedAlbum> {
    album_cache.get_next(&username.to_string()).or_else(|| {
        let client = Client::new();

        let mut albums = Vec::new();
        let mut next_url_opt = Some("https://api.spotify.com/v1/me/albums".to_string());
        while let Some(next_url) = next_url_opt {
            next_url_opt = user_state.token_checked().ok().and_then(|token| {
                client
                    .get(&next_url[..])
                    .bearer_auth(token)
                    .send()
                    .ok()
                    .and_then(|response| response.json::<Paging<SavedAlbum>>().ok())
                    .and_then(|mut page| {
                        albums.extend(page.items.drain(..));

                        page.next
                    })
            });
        }

        let mut rng = thread_rng();
        albums.shuffle(&mut rng);

        album_cache.insert(username.to_string(), albums);

        album_cache.get_next(&username.to_string())
    })
}
```
It uses the `reqwest` crate to send HTTP requests to the Spotify API and the `rand` crate to shuffle the entire collection of the user's saved albums. Finally, it returns the album as a `String` to the user. In later sections, we'll make things look prettier.
```Rust
use chashmap::CHashMap;
use rand::seq::SliceRandom;
use rand::thread_rng;
use reqwest::blocking::Client;
use rocket::http::Cookies;
use rocket::response::Redirect;
use rocket::State;
use rocket_contrib::templates::Template;

use crate::cache::Cache;
use crate::oauth::UserState;
use crate::types::{Paging, SavedAlbum};

#[get("/random_album")]
pub fn next_random_album(
    oauth_tokens: State<CHashMap<String, UserState>>,
    album_cache: State<Cache>,
    mut cookies: Cookies,
) -> Result<String, Redirect> {
    cookies
        .get_private("temp_username")
        .and_then(|username| {
            oauth_tokens
                .get_mut(username.value())
                .and_then(|mut user_state| {
                    next_album(username.value(), &album_cache, &mut user_state)
                })
        })
        .map(|album| format!("{:?}", album))
        .ok_or(Redirect::found("/error"))
}
```
# Cache
You might've noticed the `Cache` in [src/main.rs](https://github.com/jgjin/random_album/blob/master/src/main.rs) and [src/random_album.rs](https://github.com/jgjin/random_album/blob/master/src/random_album.rs). Our application uses an in-memory cache to optimize requests. Roughly, when you are thinking about cache, the order of fastest to slowest is:
1. Physical memory (RAM) (fastest)
2. Disk (files)
3. Network (slowest)

As you go down this list, you get magnitudes slower. Physical memory accesses occur in milliseconds; network accesses occur in seconds.

In general, caching relies on the fastest layers when possible and falls back upon the slower layers as necessary. In our case, we use physical memory then fall back upon network. 

To make sure previous inactive users do not crowd out new active users, cache entries have a time to live (TTL) of 12 hours. Thankfully, this is already implemented in the `ttl_cache` crate. In [src/cache.rs](https://github.com/jgjin/random_album/blob/master/src/cache.rs):
```Rust
use ttl_cache::TtlCache;

use std::collections::vec_deque::VecDeque;
use std::sync::RwLock;
use std::time::Duration;

use crate::types::SavedAlbum;

pub struct Cache {
    cache: RwLock<TtlCache<String, VecDeque<SavedAlbum>>>,
}

impl Cache {
    pub fn new() -> Self {
        Self {
            cache: RwLock::new(TtlCache::new(12)),
        }
    }

    pub fn get_next(&self, key: &String) -> Option<SavedAlbum> {
        self.cache.write().ok().and_then(|mut entries| {
            entries.get_mut(key).and_then(|saved_albums| {
                saved_albums.pop_front().map(|saved_album| {
                    saved_albums.push_back(saved_album.clone());

                    saved_album
                })
            })
        })
    }
    
    pub fn insert(&self, key: String, mut value: Vec<SavedAlbum>) {
        self.cache.write().ok().map(move |mut entries| {
            entries.insert(
                key,
                value.drain(..).collect(),
                Duration::from_secs(12 * 60 * 60),
            )
        });
    }
}
```
How did we come up with this 12 hours TTL? In practice, you select TTL by varying the length and seeing which leads to the best performance (however you define performance). In this case, I like the number 12, so....
## Readers-writer locks
Since our cache is being used by asynchronous HTTP requests, we need to support concurrency for our cache's operations. In order to do so, we use `RwLock`, a readers-writer lock. This allows either one writer or any number of readers on a shared resource at a time. [See "Hand-over-hand locking with the RAII pattern" for more details.]({{< ref "raii_hoh_locking.md" >}})
# `Option`s and `Result`s
You might've noticed a lot of `Option` and `Result` types in our Rust code.

`Option`s are either `Some` with some encapsulated value or `None`. `Result` is either `Ok` with some encapsulated value or `Err` with some encapsulated value, usually not the same type as `Ok`. 

The advantage of `Option` and `Result` is they explicitly encode the idea that something may not exist or that an operation can fail. A common pattern in other languages is to use `null` or `try`/`catch` with exceptions. However, the problem with both of these is that they are not as immediately obvious, and they don't require the caller to handle failing operations when they should. 

Also, segfaults. Did you know Rust doesn't have segfaults? That itself it pretty amazing.[^2]
[^2]: As a whole, Rust makes a trade-off of stricter compile-time errors so that common run-time errors are impossible. Even more amazing, safe (code not contained within `unsafe` blocks, you almost certainly never need to touch `unsafe` unless implementing a low-level library) Rust code won't have race conditions. Anyone who's debugged race conditions understand how great that is.
# Favicon
The favicon is that icon on the corner of each tab specific to each website. Websites specify their favicons with the `/favicon` endpoint, so we will too. In [src/assets.rs](https://github.com/jgjin/random_album/blob/master/src/assets.rs):
```Rust
use rocket::response::NamedFile;

#[get("/favicon.ico")]
pub fn favicon() -> Option<NamedFile> {
    NamedFile::open("static/favicon.ico").ok()
}
```
We put a [favicon.ico](https://github.com/jgjin/random_album/blob/master/static/favicon.ico) image in the `static` directory of our project.
# A working album selector
Finally, we have a working random album selector! Nice! Let's run `cargo run` to test it out.

Our webpage output should look something like this:
```Rust
SavedAlbum {
    added_at: "2019-09-07T21:44:53Z",
    album: Album {
        artists: [Artist {
            name: "Ariana Grande",
            uri: "spotify:artist:66CXWjxzNUsdJxJ2JdwvnR",
        }],
        images: [
            Image {
                url: "https://i.scdn.co/image/ab67616d0000b27356ac7b86e090f307e218e9c8",
            },
            Image {
                url: "https://i.scdn.co/image/ab67616d00001e0256ac7b86e090f307e218e9c8",
            },
            Image {
                url: "https://i.scdn.co/image/ab67616d0000485156ac7b86e090f307e218e9c8",
            },
        ],
        name: "thank u, next",
        release_date: "2019-02-08",
        uri: "spotify:album:2fYhqwDWXjbpjaIJPEfKFw",
    },
}
```
For clarity, I have prettified the text.
# Prettifying
Obviously, this text output isn't that pretty, so let's add some HTML templating.
## A quick overview of web content
Most web content is a combination of HTML, CSS, and JavaScript. HTML describes the structure of web content. For example, it might declare there is an image and some text. CSS describes how that content is displayed. For example, it might declare the image is 30 pixels by 50 pixels and the text is green. Finally, JavaScript describes content changes. For example, when you submit a comment on a social media post, the comment being added to the top of the comment feed is accomplished through JavaScript.

The web is full of dynamic content, content that changes over time and/or between users. We can divide dynamic content into server-side and client-side dynamic content.[^3]
[^3]: In reality, a lot of websites employ both types. 

In server-side dynamic content, the server machine is responsible for making changes before delivering content to the user (client). For example, when I go to facebook.com, the feed I see is vastly different than the feed you see, even before I interact with it.

In client-side dynamic content, the client machine is responsible for making changes. Usually client-side dynamic content is accomplished through JavaScript, so the social media comment example from before is a good example of client-side dynamic content.

I don't want to deal with JavaScript right now, so I'll be implementing server-side dynamic content through templating. In particular, I'll be using [Handlebars](https://handlebarsjs.com).
## Handlebars
In general, an HTML templating engine like Handlebars takes an HTML file marked with placeholders and renders the values in the placeholders. Here's an example in [templates/random_album.html.hbs](https://github.com/jgjin/random_album/blob/master/templates/random_album.html.hbs):
```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>
            Random Spotify album selector
        </title>

        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <link rel="stylesheet" href="/public/random_album.css" />
    </head>
    <body class="text-center">
        <div class="album-card">
            <a href="{{ album_url }}">
                <img class="mb-2 album-image" src="{{ image_url }}" alt="{{ album_title }}" width="60%" height="60%" />
            </a>
            <h2 class="h2 mb-1 font-weight-normal">{{ album_title }}</h2>
            <h5 class="h5 mb-1 font-weight-normal">{{ artists }}</h5>
            <p class="mb-1 text-muted">&copy; {{ copyright }}</p>
            <a class="btn btn-success text-white" style="width: 45%" href="/random_album">Pick another album</a>
        </div>
    </body>
</html>
```
Placeholders like `{{ album_url }}` tell Handlebars to expect a variable called `album_url` during rendering and put the value of `album_url` right there.
## Serving static and templated content
I've also created a CSS file to be used with [templates/random_album.html.hbs](https://github.com/jgjin/random_album/blob/master/templates/random_album.html.hbs). By convention, this goes in the `static` directory and is served through the `/public/<relative path to static asset from static directory>` endpoint. That is, [static/random_album.css](https://github.com/jgjin/random_album/blob/master/static/random_album.css) is served by the endpoint `/public/random_album.css`.

To add static file serving and Handlebars to Rocket, we add the following lines to [Cargo.toml](https://github.com/jgjin/random_album/blob/master/Cargo.toml):
```toml
[dependencies.rocket_contrib]
version = "0.4"
default-features = false
features = ["handlebars_templates", "serve"]
```
[src/main.rs](https://github.com/jgjin/random_album/blob/master/src/main.rs) now looks like:
```Rust
#![feature(proc_macro_hygiene, decl_macro)]

extern crate chashmap;
extern crate chrono;
extern crate dotenv;
extern crate oauth2;
extern crate rand;
extern crate reqwest;
#[macro_use]
extern crate rocket;
extern crate rocket_contrib;
extern crate serde;
extern crate ttl_cache;

use chashmap::CHashMap;
use dotenv::dotenv;
use rocket_contrib::serve::StaticFiles;
use rocket_contrib::templates::Template;

mod assets;
mod cache;
mod error;
mod oauth;
mod oauth_token;
mod random_album;
mod types;

fn main() {
    dotenv().ok();

    rocket::ignite()
        .attach(Template::fairing())
        .manage(CHashMap::<String, oauth::UserState>::new())
        .manage(cache::Cache::new())
        .mount("/public", StaticFiles::from("static"))
        .mount(
            "/",
            routes![
                assets::favicon,
                error::error,
                oauth::begin_oauth,
                oauth::end_oauth_accept,
                oauth::end_oauth_deny,
                random_album::next_random_album,
            ],
        )
        .launch();
}
```
Notice the new `attach` and `mount` calls added inside `fn main()`.

Let's now rewrite [src/random_album.rs](https://github.com/jgjin/random_album/blob/master/src/random_album.rs) to use templates:
```Rust
use chashmap::CHashMap;
use rand::seq::SliceRandom;
use rand::thread_rng;
use reqwest::blocking::Client;
use rocket::http::Cookies;
use rocket::response::Redirect;
use rocket::State;
use rocket_contrib::templates::Template;

use crate::cache::Cache;
use crate::oauth::UserState;
use crate::types::{Paging, SavedAlbum};

use std::collections::HashMap;

#[get("/random_album")]
pub fn next_random_album(
    oauth_tokens: State<CHashMap<String, UserState>>,
    album_cache: State<Cache>,
    mut cookies: Cookies,
) -> Result<Template, Redirect> {
    cookies
        .get_private("temp_username")
        .and_then(|username| {
            oauth_tokens
                .get_mut(username.value())
                .and_then(|mut user_state| {
                    next_album(username.value(), &album_cache, &mut user_state)
                })
        })
        .map(|saved_album| {
            let album = saved_album.album;

            let mut context = HashMap::new();

            context.insert("album_title", Some(album.name));
            context.insert(
                "album_url",
                album.external_urls.get("spotify").map(|url| url.clone()),
            );
            context.insert(
                "artists",
                Some(
                    album
                        .artists
                        .iter()
                        .map(|artist| artist.name.clone())
                        .collect::<Vec<String>>()
                        .join(", "),
                ),
            );
            context.insert(
                "copyright",
                album
                    .copyrights
                    .iter()
                    .filter(|copyright| copyright.copyright_type == "C")
                    .next()
                    .or(album.copyrights.first())
                    .and_then(|copyright| match &copyright.copyright_type[..] {
                        "C" => {
                            let mut cleaned = copyright.text.replace("(C)", "©");
                            if !cleaned.starts_with("©") && !cleaned.starts_with("℗") {
                                cleaned = format!("© {}", cleaned);
                            }

                            Some(cleaned)
                        }
                        "P" => {
                            let mut cleaned = copyright.text.replace("(P)", "℗");
                            if !cleaned.starts_with("℗") && !cleaned.starts_with("©") {
                                cleaned = format!("℗ {}", cleaned);
                            }

                            Some(cleaned)
                        }
                        _ => None,
                    }),
            );
            context.insert(
                "image_url",
                album.images.first().map(|image| image.url.clone()),
            );

            Template::render("random_album", context)
        })
        .ok_or(Redirect::found("/error"))
}
...
```
It's a lot of code, though most of it is formatting.

Now we can `cargo run` again and it works!
# Deploying
Now let's deploy it! I bought [jaeyoon.kim](http://jaeyoon.kim) because I have a friend with the name and I think it's funny. Domains aren't free, yet weirdly specific ones are just a few dollars.

I'm using Heroku because I found an easy way to deploy Rust apps on Heroku. [Here's the simple example I followed](https://github.com/emk/rust-buildpack-example-rocket). As long as you have the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli), it should be straightforward. To add your custom domain (such as [jaeyoon.kim](http://jaeyoon.kim)), search your domain name registrar and Heroku.

Note: make sure to update the environment variables accordingly. Consider using [Config Vars](https://devcenter.heroku.com/articles/config-vars).
# Conclusion
Oof! That's a lot of words! I try not to be too verbose. We ended up covering the following:
1. Installing Rust
2. Cargo
3. Rocket
4. OAuth
5. Rust types (`enum`s, `struct`s, and `trait`s)
6. Environment variables (`.env`)
7. `serde`
8. Pagination APIs
9. Caches
10. Concurrency (for HTTP asynchronousness) and readers-writer locks
11. `Option`s and `Result`s
12. HTML, CSS, and JavaScript
13. Dynamic web content
14. HTML templating
15. Deployment

If you've read this far, give yourself a pat on the back! And maybe a nap. You deserve it!
# Addendums
## Debugging
During testing, I set the TTL of the cache to 6 seconds and OAuth tokens to 3 seconds to quickly simulate what happens when data expires. I noticed I was encountering an error related to the Spotify API. It turns out that the Spotify API does not always return the `refresh_token` field when exchanging refresh tokens for new access tokens. The fix looks like (in [src/oauth_token.rs](https://github.com/jgjin/random_album/blob/master/src/oauth_token.rs)):
```Rust
...
pub struct OAuthToken {
    token: String,
    expiration: DateTime<Utc>,
    refresh_token: Option<String>,
}

impl TryFrom<oauth::TokenResponse> for OAuthToken {
    type Error = &'static str;

    fn try_from(token_response: oauth::TokenResponse) -> Result<Self, Self::Error> {
        Ok(Self {
            token: token_response.access_token().secret().to_string(),
            expiration: Utc::now()
                .checked_add_signed(
                    chrono::Duration::from_std(token_response.expires_in().ok_or(
                        "Spotify authorization code flow should always return expiration period!",
                    )?)
                    .map_err(|_| "Could not interpret expiration period")?,
                )
                .ok_or("Could not interpret expiration time")?,
            refresh_token: token_response
                .refresh_token()
                .map(|refresh| refresh.secret().to_string()),
        })
    }
}

impl OAuthToken {
    pub fn token_checked(&mut self) -> Result<String, &'static str> {
        if Utc::now() >= self.expiration {
            return self.refresh().map(|_| self.token.clone());
        }

        return Ok(self.token.clone());
    }

    fn refresh(&mut self) -> Result<(), &'static str> {
        self.refresh_token
            .as_ref()
            .ok_or("No refresh token found during refresh")
            .and_then(|ref_token| {
                oauth::create_client()
                    .exchange_refresh_token(&RefreshToken::new(ref_token.clone()))
                    .request(http_client)
                    .map_err(|_| "Error in refresh token request")
            })
            .and_then(|token_response| {
                Self::try_from(token_response).map(|oauth_token| {
                    self.token = oauth_token.token;
                    self.expiration = oauth_token.expiration;
                    self.refresh_token = oauth_token.refresh_token.or(self.refresh_token.clone());
                })
            })
    }
}
...
```
## Optimizing
Our application is fairly optimized. However, looking at the timings [under the network tab of Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/network), we see that we can make it even quicker by:
1. Moving the CSS (minified) into a `style` tag in [templates/random_album.html.hbs](https://github.com/jgjin/random_album/blob/master/templates/random_album.html.hbs) rather than a separate(ly loaded) file:
```HTML
...
    <head>
        <title>
            Random Spotify album selector
        </title>

        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <style>
         body,html{height:100%}body{display:flex;align-items:center;justify-content:center;padding-top:40px;padding-bottom:40px;background-color:#f5f5f5}.album-card{width:90%;max-width:1000px;padding:15px;margin:0 auto}.album-image{transition:.2s ease-in-out}.album-image:hover{filter:opacity(80%);transform:scale(1.02)}.centered{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);background-color:#1db954;height:60%;width:60%}
        </style>
    </head>
...
```
2. Lazily loading the image with the `loading` attribute of the `img` tag:
```HTML
<img class="mb-2 album-image" src="{{ image_url }}" alt="{{ album_title }}" width="60%" height="60%" loading="lazy" />
```

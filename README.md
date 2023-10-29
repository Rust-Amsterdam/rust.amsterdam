# rust.amsterdam website

This is the repository responsible for https://rust.amsterdam.

## Motivation

We're a Rust meetup, we might aswell have our website orchestrated using Rust right?

But also, we need to regularly update the QR Code for discord invites. Thus, running a pre-compiled
binary via github actions makes this a trivial task if everything is pre-compiled.

## How it works

This rust app contains some small amount of docs available in the help:

```sh
./bin/rust-amsterdam help
```

When running `rust-amsterdam build`, this app will:

- Utilise a Discord Bot to generate a new invite code for `DISCORD_CHANNEL_ID` by using the bot 
token stored in `DISCORD_BOT_TOKEN`.
- Generate a QR Code from the invite link, and render that as an svg into `build/`
- Copy all files from `static/` to `build/`

From here, the `build/` should be a static website that can be inspected using `miniserve` (`cargo
install miniserve`) by execting `make serve`.

## Discord QRCode

The join tag is generated via a bot, and is set to last from 7 to 10 days, thus the qrcode will
change regularly.

## Building

You will need to have a registered discord bot to be able to test builds from this repo. The
instructions can be found under in [the inviteify docs](inviteify/README.md).

### Using `dotenv-rust` for env vars

`.env` is git ignored so it may be easier to create `.env` file as follows:

```env
DISCORD_BOT_TOKEN=
DISCORD_CLIENT_ID=
DISCORD_CHANNEL_ID=
DISCORD_SERVER_ID=
```

Then executing via `dotenv-rust` (`cargo install dotenv --features="cli"`) as follows:

```sh
dotenv cargo run help
```

Alternatively, all the env vars can be provided via cli args - see `cargo run help`.

### Build for release

```sh
make build
```

This will geerate a new build and copy the results into `./bin/rust-amsterdam`.

Note: Make sure to check this file in when contributing since the CI/CD doesn't do a build, and
just executes this file directly.

### Manual Testing

Registering a test discord server and creating your own bot is recommended.

A link to invite your bot to your discord (assuming you have the correct vars set) can be
generated using:

```sh
dotenv cargo run bot-invite-link
```

Clicking the link will open a bot invite link in your browser. Once added, you should be able to
test the build using:

```
make build
make serve
open http://localhost:8080/index.html
```

## Contributing

Licensed under MIT. Feel free to fork, and make a pull request.

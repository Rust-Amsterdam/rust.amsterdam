# Inviteify

A rust library that can be used to manage discord invite links.

## Config

The authentication details can be generated via the [discord application portal](https://discord.com/developers/applications?new_application=true).

See [the discord docs](https://discord.com/developers/docs/getting-started) for more info.

The needed values are:

- Discord Bot Token: `https://discord.com/developers/applications/\<client_id_here\>/bot` and press
  "Reset Token" (⚠ Keep this value secret!)
- Client Id
- Server Id (AKA Guild Id)
- Channel Id

Notes:

- Server Id is only needed for generating the invite link for the bot
- Client Id/Server Id/Channel Id aren't secrets.


## Examples

The below is contrived. You should only need to register the bot once, however
it is here to show the usaged of authorization link generation.

```rust
use inviteify::Inviteify;
use std::io;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let token = std::env::var("DISCORD_BOT_TOKEN").unwrap();
    let client_id = std::env::var("DISCORD_CLIENT_ID").unwrap();
    let discord_channel_id = std::env::var("DISCORD_CHANNEL_ID").unwrap();
    let discord_server_id = std::env::var("DISCORD_SERVER_ID").unwrap();

    let mut service = Inviteify::new(&client_id, &token)?;

    println!(
        "Click this link to authorize the bot: {}",
        service.authorization_link(&discord_server_id)
    );
    println!("Press enter when ready to continue.");

    let mut buffer = String::new();
    io::stdin().read_line(&mut buffer)?;

    // Generate an invite withing the next 1->1.25 days
    let invite_code = service
        .check_and_generate_invite(&discord_channel_id, 86400, 108000)
        .await?;

    println!("Invite code: {}", invite_code);

    Ok(())
}
```

## Invite token creation logic

`check_and_generate_invite` will attempt to find a valid token using the following criteria:

- `inviter.id` is the same as the `bot.id` (e.g. it was created by the bot)
- `expires_at` is greater than the current time + `min_age`

If no matching invite, then an invite is created with the `max_age` which will be the number of
seconds in the future from now.

This can be useful to ensure invite code continuity when dealing with cron executions and to ensure
there are too many duplicate invite codes running at the same time.

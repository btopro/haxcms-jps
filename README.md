# HAX for Reclaim Cloud
Headless Authoring eXperience (HAX) is a next generation block editor that works anywhere via web components. HAXcms seeks to be the smallest possible back-end CMS to make HAX work and be able to build websites with it.

## Deploy to Reclaim Cloud
[Click here to deploy to Reclaim Cloud](https://app.my.reclaim.cloud/?app=haxcms)

## Installation Instructions

### Deploy to Reclaim Cloud
1. Click [here to deploy to Reclaim Cloud](https://app.my.reclaim.cloud/?app=haxcms).
2. The manifest downloads the latest HAXcms PHP release, enables the PHP `gd` extension, writes a one-time install token, and POSTs the admin credentials to `install.php?op=advance` — all automatically.
3. When the deploy finishes, the success panel shows your **Site URL**, **Username** (`admin`), and **Password**. Use those to log in.

### Manual / any hosting provider
HAXcms PHP is a flat-file CMS — drop the release in your webroot and visit `install.php` once.

1. Download the latest release from [haxtheweb/haxcms-php/releases](https://github.com/haxtheweb/haxcms-php/releases) and unzip it into your webroot.
2. Ensure PHP 8.3+ with the `gd` and `curl` extensions is enabled, and that the webroot is writable by the web server user.
3. Visit `https://your-domain/install.php` in a browser. The stepped wizard will:
   - **Step 1 — Status check:** verify PHP version, curl/git availability, directory writability, and HAXcms version.
   - **Step 2 — Configure:** confirm the admin username and pick a default language for new sites.
   - **Step 3 — Install:** run setup and display your auto-generated password (copy it — it is not shown again).
4. Log in at `index.php` and change your password.

**Prefer the CLI?** Run `bash scripts/haxtheweb.sh` from the webroot for a guided terminal install.

## Hosting Provider Integration
The manifest writes a one-time `_installtoken.txt` file to the webroot before
POSTing the admin credentials to `install.php?op=advance`. The installer honors
POST-supplied `user`/`pass` only when the POST includes a matching
`install_token` parameter (timing-safe comparison). While the token file
exists, every installer step requires the token, so nobody else can claim the
site first. The token file is deleted after a successful install so it cannot
be replayed. If you are integrating HAXcms with a different hosting platform,
follow the same pattern:
1. Drop a `_installtoken.txt` file containing a random secret into the webroot,
   readable by the user PHP runs as.
2. POST `toStep=4`, `user`, `pass`, and `install_token` (matching the file
   contents) to `install.php?op=advance`. The password must be 8+ characters
   with at least one uppercase letter, lowercase letter, number, and symbol.
3. Check the JSON response: a finished install returns `"step":4`; errors are
   listed in `errors`.
4. The installer deletes the token file after successful installation.

If you drop a token file but want a person to finish setup in the browser
instead, send them to `install.php?install_token=<token>`; the wizard stays
locked without it.

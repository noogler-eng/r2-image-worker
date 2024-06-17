# r2-image-worker

Store and Deliver images with Cloudflare R2 backend Cloudflare Workers.

## Synopsis

1. Deploy **r2-image-worker** to Cloudflare Workers.
1. Make base64 strings from the image file such as `.png`, `jpg`, or `gif`.
2. `PUT` the base64 strings to **r2-image-worker**.
3. An image binary will be stored in Cloudflare R2 storage.
4. **r2-image-worker** will respond the key of the stored image. `abcdef.png`
5. **r2-image-worker** serve the images on `https://r2-image-worker.username.workers.dev/abcdef.png`
6. Images will be cached on Cloudflare CDN.
7. images will be distrubted one time to CDN and then cached

```
User => Image => base64 => r2-image-worker => R2
User <= Image <= r2-image-worker <= CDN Cache <= R2
```

## Prerequisites

* Cloudflare Account
* Wrangler CLI
* (Custom domain * Cache API is not available in `*.workers.dev` domain)

## Set up


First, `git clone`

```
git clone https://github.com/yusukebe/r2-image-worker.git
cd r2-image-worker
```

Create R2 bucket:

```
wrangler r2 bucket create images
```

Copy `wrangler.example.toml` to `wrangler.toml`:

```
cp wrangler.example.toml wrangler.toml
```

Edit `wrangler.toml`.


## Variables

### Secret variables

Secret variables are:

- `USER` - User name of basic auth
- `PASS` - User password of basic auth

To set these, use `wrangler secret put` command:

```bash
wrangler secret put USER
```

## Publish

To publish to your Cloudflare Workers:

```bash
npm run deploy
```

## Endpoints

### `/upload`

Header:

To pass the Basic Auth, add the Base64 string of "user:pass" to `Authorization` header.

```
Authorization: Basic ...
```

Body:

Value of `body` is Basic64 string of image binary.

```json
{
  "body": "Base64 Text..."
}
```
### Test

1. Download a simple image

```
wget  https://www.bing.com/th?id=OHR.Unesco50_ZH-CN3652927413_UHD.jpg -O /tmp/1.jpg
```

2. Upload to u endpoint.

```
echo '{"body" : "'"$( cat /tmp/1.jpg | base64)"'"}' | curl -XPUT -H "Content-Type: application/json" -d @-  https://change_user_here:change_pass_here@change_url_here/upload -vvv
```

3. Visit the image

```
https://change_user_here:change_pass_here@change_url_here/image_returned_in_step2
```

## Use with Shortcuts

Awesome!!!

![SS](https://user-images.githubusercontent.com/10682/167978838-b3ef2d72-81ac-4058-b161-ccb2b4f0bc16.gif)

Setting shortcuts like this:

![Screenshot](https://github.com/yusukebe/r2-image-worker/assets/10682/4b028fdd-6852-42f7-8f0d-5de5e38b631b)

Shared shortcut: [Upload to Cloudflare](https://www.icloud.com/shortcuts/ab8f7d71e941461ea9e77b680e04bc4d).
Prior to utilizing it, input the domain and user:pass into the designated text field.

## Author

Yusuke Wada <https://github.com/yusukebe>

## License

MIT

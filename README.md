# Install statamic
1. "php artisan config:clear"
2. tambahkan: post-autoload-dump -> composer.json":
  "@php artisan statamic:install --ansi", 
        "@php artisan statamic:search:update --all --ansi",
        "@php artisan statamic:static:clear --ansi"
https://fly.io/app/sign-in4. Install starter kit: statamic new mysite studio1902/statamic-peak
5. php please make:user
6. Run project : php artisan serve


Ganti template: php please starter-kit:install vendor-name/starter-kit-name
jika project sudah terdapat modul pada exiting-site, tidak disarankan clear site first,
kecuali jika peoject baru

# Deploy Project Ke Fly.io
1. Buat akun Fly.io https://fly.io/app/sign-up
2. Sign in fly.io https://fly.io/app/sign-in
3. buka githbas/laragon atau sejenisnya arahkan ke folder yg akan di deploy
4. lalu ketikan perintah " flyctl auth token " untuk mendapatkan token
5. kemudian buat nama secret nya yaitu " FLY_API_TOKEN lalu masukan token ke repository secretnya di https://github.com/Alfinrizki13/Laravel-statamic/settings/secrets/actions
6. setelah itu buat folder folder di projek kalian atau sejenisnya .github/workflows/fly.yml
7. kemudian masukan kode di "fly.yml" https://github.com/DenverCoder1/tutorial-discord-bot/blob/eede71e/.github/workflows/fly.yml
8. setelah itu jalankan perintah di di terminal " flyctl launch" yes semua
9. deploy now "yes"

# Fly Documentaion
📜 Fly Documentation
Docs - https://fly.io/docs/
Fly CLI - https://fly.io/docs/flyctl/
GitHub Action - https://fly.io/docs/app-guides/contin...
Setting Environment Variables - https://fly.io/docs/reference/secrets...

# site.ext


## Installation instructions

1. run `composer install`
2. run `php please make:user`
3. run `npm i` && `npm run dev`

## Environment file contents

### Development

```env
Dump your .env values here with sensitive data removed.
```

### Production

```env
Dump your .env values here with sensitive data removed. The following is a production example that uses full static caching:
APP_NAME="Statamic Peak"
APP_ENV=production
APP_KEY=
APP_DEBUG=false
APP_URL=

DEBUGBAR_ENABLED=false

LOG_CHANNEL=stack

BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=redis
SESSION_DRIVER=file
SESSION_LIFETIME=120

REDIS_HOST=127.0.0.1
REDIS_DATABASE=
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.postmarkapp.com
MAIL_PORT=587
MAIL_ENCRYPTION=tls
MAIL_USERNAME=
MAIL_PASSWORD=
MAIL_FROM_ADDRESS=
MAIL_FROM_NAME="${APP_NAME}"

#IMAGE_MANIPULATION_DRIVER=imagick

STATAMIC_LICENSE_KEY=
STATAMIC_THEME=business

STATAMIC_API_ENABLED=false
STATAMIC_REVISIONS_ENABLED=false

STATAMIC_GIT_ENABLED=true
STATAMIC_GIT_PUSH=true
STATAMIC_GIT_DISPATCH_DELAY=5

STATAMIC_STATIC_CACHING_STRATEGY=full
SAVE_CACHED_IMAGES=false
STATAMIC_STACHE_WATCHER=false
STATAMIC_CACHE_TAGS_ENABLED=true

#STATAMIC_CUSTOM_CMS_NAME=
STATAMIC_CUSTOM_LOGO_OUTSIDE_URL="/visuals/client-logo.svg"
#STATAMIC_CUSTOM_LOGO_NAV_URL=
#STATAMIC_CUSTOM_FAVICON_URL=
#STATAMIC_CUSTOM_CSS_URL=
```

## NGINX config

Add the following to your NGINX config __inside the server block__ enable static resource caching:
```
expires $expires;
```

And this __outside the server block__:
```
map $sent_http_content_type $expires {
    default    off;
    text/css    max;
    ~image/    max;
    application/javascript    max;
    application/octet-stream    max;
}
```

## Deploy script Ploi

```bash
if [[ {COMMIT_MESSAGE} =~ "[BOT]" ]]; then
    echo "Automatically committed on production. Nothing to deploy."
    {DO_NOT_NOTIFY}
    # Uncomment the following line when using zero downtime deployments.
    # {CLEAR_NEW_RELEASE}
    exit 0
fi

cd {SITE_DIRECTORY}
git pull origin main
composer install --no-interaction --prefer-dist --optimize-autoloader --no-dev

npm ci
npm run build
{SITE_PHP} artisan cache:clear
{SITE_PHP} artisan config:cache
{SITE_PHP} artisan route:cache
{SITE_PHP} artisan statamic:stache:warm
{SITE_PHP} artisan queue:restart
{SITE_PHP} artisan statamic:search:update --all
{SITE_PHP} artisan statamic:static:clear
{SITE_PHP} artisan statamic:static:warm --queue

{RELOAD_PHP_FPM}

echo "🚀 Application deployed!"
```

## Deploy script Forge

```bash
if [[ $FORGE_DEPLOY_MESSAGE =~ "[BOT]" ]]; then
    echo "Automatically committed on production. Nothing to deploy."
    exit 0
fi

cd $FORGE_SITE_PATH
git pull origin main
$FORGE_COMPOSER install --no-interaction --prefer-dist --optimize-autoloader --no-dev

npm ci
npm run build
$FORGE_PHP artisan cache:clear
$FORGE_PHP artisan config:cache
$FORGE_PHP artisan route:cache
$FORGE_PHP artisan statamic:stache:warm
$FORGE_PHP artisan queue:restart
$FORGE_PHP artisan statamic:search:update --all
$FORGE_PHP artisan statamic:static:clear
$FORGE_PHP artisan statamic:static:warm --queue

( flock -w 10 9 || exit 1
    echo 'Restarting FPM...'; sudo -S service $FORGE_PHP_FPM reload ) 9>/tmp/fpmlock
```

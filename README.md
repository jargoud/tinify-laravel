# Tinify-laravel

Tinify API support with Laravel.

## Install

```json
{
  "require": {
    "jargoud/tinify-laravel": "^3.0"
  },
  "repositories": [
    {
      "type": "vcs",
      "url": "git@github.com:jargoud/tinify-laravel.git"
    },
    {
      "type": "vcs",
      "url": "git@github.com:jargoud/tinify-php.git"
    }
  ]
}
```

Add this to your config/app.php, 

under "providers":
```php
Jargoud\LaravelTinify\LaravelTinifyServiceProvider::class,
```
under "aliases":

```php
'Tinify' => Jargoud\LaravelTinify\Facades\Tinify::class,
```

And set a env variable `TINIFY_APIKEY` with your tinypng api key.

If you want to directly upload the image to `aws s3`, you need set the env variables of following with your aws s3 credentials.

```php
S3_KEY=
S3_SECRET=
S3_REGION=
S3_BUCKET=
```

## Examples

```php
$source = Tinify::fromFile('\path\to\file');
$source = Tinify::fromBuffer($source_data);
$source = Tinify::fromUrl($image_url);

/** To save as File **/
$result = $source->toFile('\path\to\save');

/** To get image as data **/
$data = $result->toBuffer();

Tinify::tinify('\path\to\file');
// is equivalent to
Tinify::fromFile('\path\to\file')->toFile('\path\to\file');
```

```php
$s3_result = Tinify::fileToS3('\path\to\file', $s3_bucket_name, '/path/to/save/in/bucket');

$s3_result = Tinify::bufferToS3($source_data, $s3_bucket_name, '/path/to/save/in/bucket');

$s3_result = Tinify::urlToS3($image_url, $s3_bucket_name, '/path/to/save/in/bucket');

/** To get the url of saved image **/
$s3_image_url = $s3_result->location();
$s3_image_width = $s3_result->width();
$s3_image_hight = $s3_result->height();
```

`NOTE:` All the images directly save to s3 is publicably readable. And you can set permissions for s3 bucket folder in your aws console to make sure the privacy of images.

A [job](./src/Jobs/Tinify.php) is provided to do asynchronous compression, it can be used with the following code:

```php
use Illuminate\Contracts\Bus\Dispatcher;
use Jargoud\LaravelTinify\Jobs\Tinify;

app(Dispatcher::class)->dispatch(
    new Tinify($sourcePath)
);
```

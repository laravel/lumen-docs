# Filesystem / Cloud Storage

- [Introduction](#introduction)
- [Configuration](#configuration)
- [Basic Usage](#basic-usage)

<a name="introduction"></a>
## Introduction

Lumen provides a wonderful filesystem abstraction thanks to the [Flysystem](https://github.com/thephpleague/flysystem) PHP package by Frank de Jonge. The Flysystem integration provides simple to use drivers for working with local filesystems, Amazon S3, and Rackspace Cloud Storage. Even better, it's amazingly simple to switch between these storage options as the API remains the same for each system!

<a name="configuration"></a>
## Configuration

The filesystem configuration options are located in your `.env` configuration file. You may look at the `.env.example` configuration file for an example of using these options.

Before using the S3 or Rackspace drivers, you will need to install the appropriate package via Composer:

- Amazon S3: `league/flysystem-aws-s3-v2 ~1.0`
- Rackspace: `league/flysystem-rackspace ~1.0`

When using the `local` driver, note that all file operations are relative to the `storage/app` directory. Therefore, the following method would store a file in `storage/app/file.txt`:

	Storage::disk('local')->put('file.txt', 'Contents');

<a name="basic-usage"></a>
## Basic Usage

> **Note:** If you intend to use the `Storage` facade, be sure to uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

The `Storage` facade may be used to interact with any of your configured disks. Alternatively, you may type-hint the `Illuminate\Contracts\Filesystem\Factory` contract on any class that is resolved via the Laravel [service container](/docs/container).

#### Retrieving A Particular Disk

	$disk = Storage::disk('s3');

	$disk = Storage::disk('local');

#### Determining If A File Exists

	$exists = Storage::disk('s3')->exists('file.jpg');

#### Calling Methods On The Default Disk

	if (Storage::exists('file.jpg')) {
		//
	}

#### Retrieving A File's Contents

	$contents = Storage::get('file.jpg');

#### Setting A File's Contents

	Storage::put('file.jpg', $contents);

#### Prepend To A File

	Storage::prepend('file.log', 'Prepended Text');

#### Append To A File

	Storage::append('file.log', 'Appended Text');

#### Delete A File

	Storage::delete('file.jpg');

	Storage::delete(['file1.jpg', 'file2.jpg']);

#### Copy A File To A New Location

	Storage::copy('old/file1.jpg', 'new/file1.jpg');

#### Move A File To A New Location

	Storage::move('old/file1.jpg', 'new/file1.jpg');

#### Get File Size

	$size = Storage::size('file1.jpg');

#### Get The Last Modification Time (UNIX)

	$time = Storage::lastModified('file1.jpg');

#### Get All Files Within A Directory

	$files = Storage::files($directory);

	// Recursive...
	$files = Storage::allFiles($directory);

#### Get All Directories Within A Directory

	$directories = Storage::directories($directory);

	// Recursive...
	$directories = Storage::allDirectories($directory);

#### Create A Directory

	Storage::makeDirectory($directory);

#### Delete A Directory

	Storage::deleteDirectory($directory);

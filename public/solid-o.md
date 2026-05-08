# OCP (Open/Closed Principle) in Laravel

## What is OCP?

OCP means:

> Open for extension
> Closed for modification

---

# Simple Meaning

You should be able to ADD new features
WITHOUT changing old working code.

---

# Real-Life Example

A mobile charger supports:

* Android cable
* iPhone cable
* Type-C cable

without changing the phone itself.

Phone is:

```text
Closed for modification
```

But:

```text
Open for extension
```

because new cables can be added.

---

# ❌ Without OCP (Bad Example)

## UserController.php

```php
<?php

namespace App\Http\Controllers;

class UserController extends Controller
{
    public function upload()
    {
        $storage = "local";

        if($storage == "local") {

            return "Upload To Local";

        } elseif($storage == "s3") {

            return "Upload To AWS S3";

        } elseif($storage == "cloudinary") {

            return "Upload To Cloudinary";
        }
    }
}
```

---

# Problems

Every time a new storage system is added:

* controller changes
* more if/else added
* code becomes messy

This violates OCP.

---

# ✅ With OCP (Good Example)

We use:

* Interface
* Multiple services

---

# ImageServiceInterface.php

```php
<?php

namespace App\Contracts;

interface ImageServiceInterface
{
    public function upload(): string;
}
```

### Responsibility:

* Define upload rules

---

# LocalImageService.php

```php
<?php

namespace App\Services;

use App\Contracts\ImageServiceInterface;

class LocalImageService implements ImageServiceInterface
{
    public function upload(): string
    {
        return "Upload To Local Storage";
    }
}
```

### Responsibility:

* Upload to local storage

---

# S3ImageService.php

```php
<?php

namespace App\Services;

use App\Contracts\ImageServiceInterface;

class S3ImageService implements ImageServiceInterface
{
    public function upload(): string
    {
        return "Upload To AWS S3";
    }
}
```

### Responsibility:

* Upload to AWS S3

---

# CloudinaryImageService.php

```php
<?php

namespace App\Services;

use App\Contracts\ImageServiceInterface;

class CloudinaryImageService implements ImageServiceInterface
{
    public function upload(): string
    {
        return "Upload To Cloudinary";
    }
}
```

### Responsibility:

* Upload to Cloudinary

---

# UserController.php

```php
<?php

namespace App\Http\Controllers;

use App\Contracts\ImageServiceInterface;

class UserController extends Controller
{
    public function __construct(
        protected ImageServiceInterface $imageService
    ) {}

    public function upload()
    {
        return $this->imageService->upload();
    }
}
```

### Responsibility:

* Call upload service only

---

# AppServiceProvider.php

## Local Storage

```php
$this->app->bind(
    ImageServiceInterface::class,
    LocalImageService::class
);
```

---

## AWS S3

```php
$this->app->bind(
    ImageServiceInterface::class,
    S3ImageService::class
);
```

---

## Cloudinary

```php
$this->app->bind(
    ImageServiceInterface::class,
    CloudinaryImageService::class
);
```

---

# Final Flow

```text
Route
 ↓
Controller
 ↓
ImageServiceInterface
 ↓
Selected Service
 ↓
upload()
```

---

# Why This Follows OCP

Controller never changes.

We only ADD new services.

System is:

```text
Open for extension
```

because new services can be added.

And:

```text
Closed for modification
```

because old controller code stays untouched.

---

# Benefits of OCP

* Cleaner architecture
* Easy future upgrades
* Less risky changes
* Flexible system
* Better maintenance
* Reusable services

---

# Real Laravel Examples

Laravel uses OCP everywhere:

| Feature | Extensions               |
| ------- | ------------------------ |
| Cache   | Redis / File / Memcached |
| Mail    | SMTP / SES / Mailgun     |
| Queue   | Database / Redis / SQS   |
| Storage | Local / S3 / FTP         |

---

# Simple Meaning

```text
Add new functionality
WITHOUT changing old stable code.
```

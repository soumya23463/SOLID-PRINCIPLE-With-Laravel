# DIP (Dependency Inversion Principle) in Laravel

## What is DIP?

DIP means:

> High-level classes should not depend on low-level classes.
> Both should depend on abstractions (interfaces).

---

# Simple Meaning

Classes should depend on interfaces/contracts,

NOT directly on concrete classes.

---

# Real-Life Example

A switch can control:

* fan
* bulb
* AC

Switch does not care:

* which company
* which device

It only uses a standard socket/interface.

---

# ❌ Bad Example (Without DIP)

## UserController.php

```php id="dipbad1"
<?php

namespace App\Http\Controllers;

use App\Services\LocalImageService;

class UserController extends Controller
{
    protected $imageService;

    public function __construct()
    {
        $this->imageService =
            new LocalImageService();
    }

    public function upload()
    {
        return $this->imageService->upload();
    }
}
```

---

# Problems

Controller directly depends on:

```php id="dipbad2"
LocalImageService
```

This creates:

* tight coupling
* hardcoded dependency
* difficult future changes

If you want:

* AWS S3
* Cloudinary

Controller must change again.

This violates DIP.

---

# ✅ Good Example (With DIP)

Use interface abstraction.

---

# ImageServiceInterface.php

```php id="dipinterface"
<?php

namespace App\Contracts;

interface ImageServiceInterface
{
    public function upload(): string;
}
```

### Responsibility:

* Define upload contract

---

# LocalImageService.php

```php id="diplocal"
<?php

namespace App\Services;

use App\Contracts\ImageServiceInterface;

class LocalImageService
implements ImageServiceInterface
{
    public function upload(): string
    {
        return "Uploaded To Local Storage";
    }
}
```

### Responsibility:

* Upload to local storage

---

# S3ImageService.php

```php id="dips3"
<?php

namespace App\Services;

use App\Contracts\ImageServiceInterface;

class S3ImageService
implements ImageServiceInterface
{
    public function upload(): string
    {
        return "Uploaded To AWS S3";
    }
}
```

### Responsibility:

* Upload to AWS S3

---

# CloudinaryImageService.php

```php id="dipcloud"
<?php

namespace App\Services;

use App\Contracts\ImageServiceInterface;

class CloudinaryImageService
implements ImageServiceInterface
{
    public function upload(): string
    {
        return "Uploaded To Cloudinary";
    }
}
```

### Responsibility:

* Upload to Cloudinary

---

# UserController.php

```php id="dipcontroller"
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

* Use upload service only

---

# AppServiceProvider.php

## Local Storage

```php id="dipbind1"
$this->app->bind(
    ImageServiceInterface::class,
    LocalImageService::class
);
```

---

## AWS S3

```php id="dipbind2"
$this->app->bind(
    ImageServiceInterface::class,
    S3ImageService::class
);
```

---

## Cloudinary

```php id="dipbind3"
$this->app->bind(
    ImageServiceInterface::class,
    CloudinaryImageService::class
);
```

---

# Final Flow

```text id="dipflow"
Controller
 ↓
Interface
 ↓
Selected Service
 ↓
upload()
```

---

# Why This Follows DIP

Controller depends on:

```php id="dipabstraction"
ImageServiceInterface
```

NOT:

* LocalImageService
* S3ImageService
* CloudinaryImageService

This creates loose coupling.

---

# Laravel Automatically Injects Dependency

Laravel sees:

```php id="dipinject1"
ImageServiceInterface
```

Then checks binding:

```php id="dipinject2"
Interface → Concrete Class
```

and automatically injects the correct service.

---

# Benefits of DIP

* Flexible architecture
* Easy future upgrades
* Easy testing
* Less coupling
* Better maintainability
* Cleaner code

---

# Real Laravel Examples

Laravel uses DIP everywhere:

| Feature | Interface           |
| ------- | ------------------- |
| Cache   | Cache Repository    |
| Queue   | Queue Contract      |
| Mail    | Mail Contract       |
| Events  | Dispatcher Contract |

---

# Difference Between OCP and DIP

| OCP                    | DIP                  |
| ---------------------- | -------------------- |
| Add new classes easily | Depend on interfaces |
| Extension principle    | Dependency principle |

---

# Simple Meaning

```text id="dipsimple"
Depend on abstraction,
not concrete classes.
```

---

# Final Definition

```text id="dipfinal"
High-level classes should depend on interfaces,
not directly on concrete classes.
```

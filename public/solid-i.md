# ISP (Interface Segregation Principle) in Laravel

## What is ISP?

ISP means:

> Do not force classes to implement methods they do not use.

---

# Simple Meaning

Instead of one huge interface,

create small focused interfaces.

---

# ❌ Bad Example (Without ISP)

## WorkerInterface.php

```php id="ispbad1"
<?php

interface WorkerInterface
{
    public function cook();

    public function drive();

    public function code();
}
```

---

# Chef.php

```php id="ispbad2"
<?php

class Chef implements WorkerInterface
{
    public function cook()
    {
        return "Cooking";
    }

    public function drive()
    {
        // unnecessary
    }

    public function code()
    {
        // unnecessary
    }
}
```

---

# Problem

Chef only cooks.

Why force:

* drive()
* code()

This violates ISP.

---

# ✅ Good Example (With ISP)

Split interfaces into smaller parts.

---

# CookInterface.php

```php id="ispgood1"
<?php

interface CookInterface
{
    public function cook();
}
```

---

# DriveInterface.php

```php id="ispgood2"
<?php

interface DriveInterface
{
    public function drive();
}
```

---

# CodeInterface.php

```php id="ispgood3"
<?php

interface CodeInterface
{
    public function code();
}
```

---

# Chef.php

```php id="ispgood4"
<?php

class Chef implements CookInterface
{
    public function cook()
    {
        return "Cooking";
    }
}
```

Perfect ✅

Chef only implements what it needs.

---

# Laravel Example

Suppose image upload system.

---

# ❌ Bad ImageServiceInterface.php

```php id="ispbadlaravel1"
<?php

interface ImageServiceInterface
{
    public function upload();

    public function resize();

    public function compress();

    public function watermark();
}
```

---

# Problem

Some services may only upload.

But all services are forced to implement:

* resize()
* compress()
* watermark()

This violates ISP.

---

# ❌ Bad LocalImageService.php

```php id="ispbadlaravel2"
<?php

class LocalImageService implements ImageServiceInterface
{
    public function upload()
    {
        return "Uploaded";
    }

    public function resize()
    {
        // unnecessary
    }

    public function compress()
    {
        // unnecessary
    }

    public function watermark()
    {
        // unnecessary
    }
}
```

---

# ✅ Good Laravel ISP Example

Create small focused interfaces.

---

# UploadInterface.php

```php id="ispupload"
<?php

interface UploadInterface
{
    public function upload();
}
```

---

# ResizeInterface.php

```php id="ispresize"
<?php

interface ResizeInterface
{
    public function resize();
}
```

---

# CompressInterface.php

```php id="ispcompress"
<?php

interface CompressInterface
{
    public function compress();
}
```

---

# LocalImageService.php

```php id="isplocal"
<?php

class LocalImageService implements UploadInterface
{
    public function upload()
    {
        return "Uploaded";
    }
}
```

Perfect ✅

Only upload responsibility.

---

# AdvancedImageService.php

```php id="ispadvanced"
<?php

class AdvancedImageService implements
    UploadInterface,
    ResizeInterface,
    CompressInterface
{
    public function upload()
    {
        return "Uploaded";
    }

    public function resize()
    {
        return "Resized";
    }

    public function compress()
    {
        return "Compressed";
    }
}
```

---

# One Method Can Work for Multiple Interfaces

## UploadInterface.php

```php id="ispmulti1"
<?php

interface UploadInterface
{
    public function upload();
}
```

---

## ImageInterface.php

```php id="ispmulti2"
<?php

interface ImageInterface
{
    public function upload();
}
```

---

# ImageService.php

```php id="ispmulti3"
<?php

class ImageService implements
    UploadInterface,
    ImageInterface
{
    public function upload()
    {
        return "Image Uploaded";
    }
}
```

---

# What Happened?

One method:

```php id="ispmulti4"
upload()
```

satisfies both interfaces.

---

# Final Flow

```text id="ispflow"
Controller
 ↓
Small Specific Interfaces
 ↓
Selected Service
 ↓
Required Methods Only
```

---

# Benefits of ISP

* Cleaner interfaces
* Less unnecessary code
* Better maintainability
* Flexible architecture
* Easier testing
* Easier scaling

---

# Real Laravel Examples

Laravel uses ISP in many places:

| Interface   | Responsibility   |
| ----------- | ---------------- |
| Responsable | HTTP response    |
| ShouldQueue | Queue jobs       |
| Arrayable   | Convert to array |
| Jsonable    | Convert to JSON  |

Small focused interfaces.

---

# Simple Meaning

```text id="ispsimple"
Small specific interfaces are better
than one huge interface.
```

---

# Final Definition

```text id="ispfinal"
Do not force a class
to implement methods it does not use.
```

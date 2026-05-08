# SRP (Single Responsibility Principle) in Laravel

## What is SRP?

SRP means:

> One class should have only one responsibility.

OR

> One class should do one job only.

---

# ❌ Without SRP (Bad Example)

## UserController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;
use Mail;

class UserController extends Controller
{
    public function store(Request $request)
    {
        // Validation
        $request->validate([
            'name' => 'required',
            'email' => 'required'
        ]);

        // Save User
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email
        ]);

        // Upload Image
        if($request->hasFile('image'))
        {
            $imageName = time().'.'.$request->image->extension();

            $request->image->move(
                public_path('images'),
                $imageName
            );
        }

        // Send Email
        Mail::raw('Welcome User', function($message) use ($user) {

            $message->to($user->email)
                    ->subject('Welcome');

        });

        return "User Registered";
    }
}
```

---

# Problems

This controller is doing many jobs:

* Validation
* Save user
* Upload image
* Send email

One file became too big and messy.

If upload logic changes, we edit controller.
If email logic changes, we edit controller again.

This violates SRP.

---

# ✅ With SRP (Good Example)

Now we divide responsibilities into separate classes.

---

# UserController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Services\UserService;

class UserController extends Controller
{
    protected $userService;

    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    public function store(Request $request)
    {
        $this->userService->createUser($request);

        return "User Registered";
    }
}
```

### Responsibility:

* Handle request only

---

# UserService.php

```php
<?php

namespace App\Services;

use App\Models\User;
use App\Services\ImageService;
use App\Services\EmailService;

class UserService
{
    protected $imageService;
    protected $emailService;

    public function __construct(
        ImageService $imageService,
        EmailService $emailService
    )
    {
        $this->imageService = $imageService;
        $this->emailService = $emailService;
    }

    public function createUser($request)
    {
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email
        ]);

        if($request->hasFile('image'))
        {
            $this->imageService->upload($request->image);
        }

        $this->emailService->sendWelcomeMail($user);

        return $user;
    }
}
```

### Responsibility:

* User business logic

---

# ImageService.php

```php
<?php

namespace App\Services;

class ImageService
{
    public function upload($image)
    {
        $imageName = time().'.'.$image->extension();

        $image->move(
            public_path('images'),
            $imageName
        );
    }
}
```

### Responsibility:

* Upload image only

---

# EmailService.php

```php
<?php

namespace App\Services;

use Mail;

class EmailService
{
    public function sendWelcomeMail($user)
    {
        Mail::raw('Welcome User', function($message) use ($user) {

            $message->to($user->email)
                    ->subject('Welcome');

        });
    }
}
```

### Responsibility:

* Send email only

---

# Final Flow

```text
Route
 ↓
Controller
 ↓
UserService
 ↓
ImageService
 ↓
EmailService
```

---

# Benefits of SRP

* Cleaner code
* Smaller files
* Easy debugging
* Easy maintenance
* Easy reuse
* Better teamwork

---

# Simple Meaning

SRP means:

```text
One class = One responsibility
```

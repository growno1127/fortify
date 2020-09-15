<p align="center"><img src="https://laravel.com/assets/img/components/logo-fortify.svg"></p>

<p align="center">
    <a href="https://github.com/laravel/fortify/actions">
        <img src="https://github.com/laravel/fortify/workflows/tests/badge.svg" alt="Build Status">
    </a>
    <a href="https://packagist.org/packages/laravel/fortify">
        <img src="https://poser.pugx.org/laravel/fortify/d/total.svg" alt="Total Downloads">
    </a>
    <a href="https://packagist.org/packages/laravel/fortify">
        <img src="https://poser.pugx.org/laravel/fortify/v/stable.svg" alt="Latest Stable Version">
    </a>
    <a href="https://packagist.org/packages/laravel/fortify">
        <img src="https://poser.pugx.org/laravel/fortify/license.svg" alt="License">
    </a>
</p>

## Introduction

Laravel Fortify is a frontend agnostic authentication backend for Laravel. Fortify powers the registration, authentication, and two-factor authentication features of [Laravel Jetstream](https://github.com/laravel/jetstream).

## Using Fortify For Headless Authentication

You may use Fortify (without Jetstream) to serve a headless authentication backend for your Laravel application. In this scenario, you are required to build your own templates using the frontend stack of your choice (Blade, Vue, etc.)

- [Installation](#installation)
- [The Fortify Service Provider](#the-fortify-service-provider)
- [Authentication](#authentication)
    - [Customizing User Authentication](#customizing-user-authentication)
- [Registration](#registration)
    - [Customizing Registration](#customizing-registration)

<a name="installation"></a>
### Installation

To get started, install Fortify using Composer:

```bash
composer require laravel/fortify
```

Next, publish Fortify's resources:

```bash
php artisan vendor:publish --provider="Laravel\\Fortify\\FortifyServiceProvider"
```

This command will publish Fortify's actions to your `app/Actions` directory. This directory will be created if it does not exist. In addition, Fortify's configuration file and migrations will be published.

Next, you should migrate your database:

```bash
php artisan migrate
```

<a name="the-fortify-service-provider"></a>
#### The Fortify Service Provider

The `vendor:publish` command discussed above will also publish the `app/Providers/FortifyServiceProvider` file. You should ensure this file is registered within the `providers` array of your `app` configuration file.

This service provider registers the actions that Fortify published, instructing Fortify to use them when their respective tasks are executed by Fortify.

<a name="authentication"></a>
### Authentication

To get started, we need to instruct Fortify how to return our `login` view. Remember, Fortify is a headless authentication library. If you would like a frontend implementation of Fortify that is already completed for you, you should use [Laravel Jetstream](https://jetstream.laravel.com).

All of the authentication view's rendering logic may be customized using the appropriate methods available via the `Laravel\Fortify\Fortify` class. Typically, you should call this method from the `boot` method of your `FortifyServiceProvider`:

```php
use Laravel\Fortify\Fortify;

Fortify::loginView(function () {
    return view('auth.login');
});
```

Fortify will take care of generating the `/login` route that returns this view. Your `login` template should include a form that makes a POST request to `/login`. The `/login` action expects a string email address / username and a `password`. The name of the email / username field should match the `username` value of the `fortify` configuration file.

If the login attempt is successful, Fortify will redirect you to the URI configured via the `home` configuration option within your `fortify` configuration file. If the login request was an XHR request, a `200` HTTP response will be returned.

<a name="customizing-user-authentication"></a>
#### Customizing User Authentication

Sometimes, you may wish to have full customization over how login credentials are authenticated and users are retrieved. Thankfully, Fortify allows you to easily accomplish this using the `Fortify::authenticateUsing` method.

This method accepts a Closure which that receives the incoming HTTP request. The Closure is responsible for validating the login credentials attached to the request and returning the associated user instance. If the credentials are invalid or no user can be found, `null` or `false` should be returned by the Closure. Typically, this method should be called from the `boot` method of your `FortifyServiceProvider`:

```php
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Laravel\Fortify\Fortify;

Fortify::authenticateUsing(function (Request $request) {
    $user = User::where('email', $request->email)->first();

    if ($user &&
        Hash::check($request->password, $user->password)) {
        return $user;
    }
})
```

<a name="registration"></a>
### Registration

To begin implementing registration functionality, we need to instruct Fortify how to return our `register` view. Remember, Fortify is a headless authentication library. If you would like a frontend implementation of Fortify that is already completed for you, you should use [Laravel Jetstream](https://jetstream.laravel.com).

All of the authentication view's rendering logic may be customized using the appropriate methods available via the `Laravel\Fortify\Fortify` class. Typically, you should call this method from the `boot` method of your `FortifyServiceProvider`:

```php
use Laravel\Fortify\Fortify;

Fortify::registerView(function () {
    return view('auth.register');
});
```

Fortify will take care of generating the `/register` route that returns this view. Your `login` template should include a form that makes a POST request to `/register`. The `/register` action expects a string `name`, string email address / username, `password`, and `password_confirmation` fields. The name of the email / username field should match the `username` value of the `fortify` configuration file.

If the registration attempt is successful, Fortify will redirect you to the URI configured via the `home` configuration option within your `fortify` configuration file. If the login request was an XHR request, a `200` HTTP response will be returned.

<a name="customizing-registration"></a>
#### Customizing Registration

The user validation and creation process may be customized by modifying the `App\Actions\CreateNewUser` action.

## Contributing

Thank you for considering contributing to Fortify! You can read the contribution guide [here](.github/CONTRIBUTING.md).

## Code of Conduct

In order to ensure that the Laravel community is welcoming to all, please review and abide by the [Code of Conduct](https://laravel.com/docs/contributions#code-of-conduct).

## Security Vulnerabilities

Please review [our security policy](https://github.com/laravel/fortify/security/policy) on how to report security vulnerabilities.

## License

Laravel Fortify is open-sourced software licensed under the [MIT license](LICENSE.md).

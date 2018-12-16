<p align="center"><img src="https://laravel.com/assets/img/components/logo-laravel.svg"></p>

<p align="center">
<a href="https://travis-ci.org/laravel/framework"><img src="https://travis-ci.org/laravel/framework.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/license.svg" alt="License"></a>
</p>

## Laravel 5.7
Laravel 5.7 continues the improvements made in Laravel 5.6 by introducing Laravel Nova, optional email verification to the authentication scaffolding, support for guest users in authorization gates and policies, console testing improvements, Symfony dump-server integration, localizable notifications, and a variety of other bug fixes and usability improvements.

## Laravel Nova
Laravel Nova is a beautiful, best-in-class administration dashboard for Laravel applications. Of course, the primary feature of Nova is the ability to administer your underlying database records using Eloquent. Additionally, Nova offers support for filters, lenses, actions, queued actions, metrics, authorization, custom tools, custom cards, custom fields, and more.

To learn more about Laravel Nova, check out the Nova website.

## Email Verification
Laravel 5.7 introduces optional email verification to the authentication scaffolding included with the framework. To accommodate this feature, an email_verified_at timestamp column has been added to the default users table migration that is included with the framework.

To prompt newly registered users to verify their email, the User model should be marked with the MustVerifyEmail interface:

<?php

namespace App;

use Illuminate\Notifications\Notifiable;
use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements MustVerifyEmail
{
    // ...
}
Once the User model is marked with the MustVerifyEmail interface, newly registered users will receive an email containing a signed verification link. Once this link has been clicked, Laravel will automatically record the verification time in the database and redirect users to a location of your choosing.

A verified middleware has been added to the default application's HTTP kernel. This middleware may be attached to routes that should only allow verified users:

'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
To learn more about email verification, check out the complete documentation.

Guest User Gates / Policies
In previous versions of Laravel, authorization gates and policies automatically returned false for unauthenticated visitors to your application. However, you may now allow guests to pass through authorization checks by declaring an "optional" type-hint or supplying a null default value for the user argument definition:

Gate::define('update-post', function (?User $user, Post $post) {
    // ...
});

## Symfony Dump Server
Laravel 5.7 offers integration with Symfony's dump-server command via a package by Marcel Pociot. To get started, run the dump-server Artisan command:

php artisan dump-server
Once the server has started, all calls to dump will be displayed in the dump-server console window instead of in your browser, allowing you to inspect the values without mangling your HTTP response output.

Notification Localization
Laravel now allows you to send notifications in a locale other than the current language, and will even remember this locale if the notification is queued.

To accomplish this, the Illuminate\Notifications\Notification class now offers a locale method to set the desired language. The application will change into this locale when the notification is being formatted and then revert back to the previous locale when formatting is complete:

$user->notify((new InvoicePaid($invoice))->locale('es'));
Localization of multiple notifiable entries may also be achieved via the Notification facade:

Notification::locale('es')->send($users, new InvoicePaid($invoice));
Console Testing
Laravel 5.7 allows you to easily "mock" user input for your console commands using the  expectsQuestion method. In addition, you may specify the exit code and text that you expect to be output by the console command using the assertExitCode and expectsOutput methods. For example, consider the following console command:

Artisan::command('question', function () {
    $name = $this->ask('What is your name?');

    $language = $this->choice('Which language do you program in?', [
        'PHP',
        'Ruby',
        'Python',
    ]);

    $this->line('Your name is '.$name.' and you program in '.$language.'.');
});
You may test this command with the following test which utilizes the expectsQuestion,  expectsOutput, and assertExitCode methods:

/**
 * Test a console command.
 *
 * @return void
 */
public function test_console_command()
{
    $this->artisan('question')
         ->expectsQuestion('What is your name?', 'Taylor Otwell')
         ->expectsQuestion('Which language do you program in?', 'PHP')
         ->expectsOutput('Your name is Taylor Otwell and you program in PHP.')
         ->assertExitCode(0);
}
URL Generator & Callable Syntax
Instead of only accepting strings, Laravel's URL generator now accepts "callable" syntax when generating URLs to controller actions:

action([UserController::class, 'index']);
Paginator Links
Laravel 5.7 allows you to control how many additional links are displayed on each side of the paginator's URL "window". By default, three links are displayed on each side of the primary paginator links. However, you may control this number using the onEachSide method:

{{ $paginator->onEachSide(5)->links() }}
Filesystem Read / Write Streams
Laravel's Flysystem integration now offers readStream and writeStream methods:

Storage::disk('s3')->writeStream(
    'remote-file.zip',
    Storage::disk('local')->readStream('local-file.zip')
);

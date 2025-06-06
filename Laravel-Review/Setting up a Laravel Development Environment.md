# Setting up a Laravel Development Environment

To start building applications with Laravel, you'll need to set up a suitable development environment on your computer.

## Core Requirements

Ensure you have the following installed:

*   **PHP:** Laravel's required PHP version changes with its releases. Always check the official documentation for the version compatible with the Laravel version you intend to use (typically, recent stable PHP versions are recommended).
*   **Composer:** Laravel uses Composer to manage its dependencies. Composer is a tool for dependency management in PHP. You can download it from [getcomposer.org](https://getcomposer.org/).
*   **Database:** Laravel supports several databases out of the box:
    *   MySQL
    *   PostgreSQL
    *   SQLite
    *   SQL Server
    You'll need one of these installed and running.
*   **Web Server (Optional for local development):** While traditional setups use web servers like Nginx or Apache, Laravel comes with a built-in development server (`php artisan serve`) which is convenient for local development.

## Laravel Installation

The most common way to create a new Laravel project is using Composer:

```bash
composer create-project laravel/laravel your-project-name
```
Replace `your-project-name` with the desired name for your project directory. This command downloads the Laravel framework and all its dependencies.

## Local Development Environment Options

Several options exist for running Laravel locally:

*   **Laravel Sail:**
    *   A light-weight command-line interface for interacting with Laravel's default Docker development environment.
    *   Provides a great way to get started with Laravel without needing to install PHP, a web server, or other software directly on your local machine, as Docker handles all of that in containers.
    *   Included with new Laravel applications.

*   **Laravel Homestead:**
    *   An official, pre-packaged Vagrant box.
    *   Provides a complete, virtualized development environment with PHP, Nginx, MySQL, Redis, Memcached, Node, and more.
    *   Ensures a consistent environment across different operating systems and among team members.

*   **Valet (macOS only):**
    *   An extremely fast Laravel development environment for Mac users.
    *   It configures your Mac to always run Nginx in the background. Then, using DnsMasq, Valet proxies all requests on the `*.test` domain to point to sites installed on your local machine.

*   **Manual Setup (e.g., MAMP, WAMP, XAMPP, or native installations):**
    *   You can also manually install PHP, a web server, and a database on your operating system.
    *   Tools like MAMP (for macOS), WAMP (for Windows), or XAMPP (cross-platform) bundle these together.

Once your project is created and your environment is set up, you can typically navigate to your project directory in the terminal and start Laravel's local development server:

```bash
php artisan serve
```
This will usually make your application accessible at `http://localhost:8000`.

---
[Back to Laravel Basics Index](https://hackmd.io/@jmrecodes/B1PTGFkXgl)
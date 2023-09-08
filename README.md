<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

# Invoice Application

This is a simple invoice application built with Vue.js and Laravel. It allows users to create, edit, and view invoices.

## Features

* Create invoices with a variety of information, including customer name, date, due date, number, reference, items, unit price, quantity, and total.
* Edit and view invoices.
* Export invoices to PDF.

## Requirements

* Node.js
* npm
* Laravel

## Installation

1. Clone the repository to your local machine.
2. Install the dependencies with `npm install`.
3. Create a new Laravel project with `laravel new invoice_application`.
4. Copy the contents of the `invoice_application` directory into the new Laravel project.
5. Update the `.env` file with your database connection information.
6. Run `php artisan migrate` to create the database tables.
7. Run `php artisan serve` to start the development server.

## Usage

Open the application in your web browser at `http://localhost:8000`.

To create a new invoice, click the "Create Invoice" button. Enter the necessary information and click the "Create" button.

To edit an invoice, click the "Edit" button next to the invoice. Make your changes and click the "Update" button.

To view an invoice, click the "View" button next to the invoice.

To export an invoice to PDF, click the "Export" button next to the invoice.

## Testing

To run the tests, run `php artisan test`.

## Contributing

Contributions are welcome. Please open an issue or pull request if you have any changes or improvements to suggest.

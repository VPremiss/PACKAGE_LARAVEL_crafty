<div align="center">
    بسم الله الرحمن الرحيم
</div>

# Crafty

Some essential helpers to rely on during [TALL stack](https://tallstack.dev) development.

[![Latest Version on Packagist](https://img.shields.io/packagist/v/vpremiss/crafty.svg?style=for-the-badge&color=gray)](https://packagist.org/packages/vpremiss/crafty)
[![GitHub Tests Action Status](https://img.shields.io/github/actions/workflow/status/vpremiss/crafty/testing-and-analysis.yml?branch=main&label=tests&style=for-the-badge&color=forestgreen)](https://github.com/VPremiss/PACKAGE_LARAVEL_crafty/actions/workflows/testing-and-analysis.yml?query=branch%3Amain++)
![Codecov](https://img.shields.io/codecov/c/github/VPremiss/Crafty?style=for-the-badge&color=purple)
[![Total Downloads](https://img.shields.io/packagist/dt/vpremiss/crafty.svg?style=for-the-badge&color=blue)](https://packagist.org/packages/vpremiss/crafty)


## Description

Contains some helper service classes (that you can use as [Laravel facades](https://laravel.com/docs/facades)). And it has some global helper functions for quick actions as well... Pretty much the things we found ourselves needing and without having a "strong" reason to PR Laravel about.


## Installation

1. Install the package via [composer](https://getcomposer.org):

   ```bash
   composer require VPremiss/Crafty
   ```

2. Publish the [config file](config/crafty.php) using this [Artisan](https://laravel.com/docs/artisan) command:

   ```bash
   php artisan vendor:publish --tag="crafty-config"
   ```

### Upgrading

1. Backup your current [config](config/crafty.php).

2. Republish the package stuff using this Artisan command:

   ```bash
   php artisan vendor:publish --tag="crafty-config" --force
   ```


## Usage

- **Enumerified**
  - Can be applied to enums to extend their TALL abilities.

- **Installable**
  - Used along `HasInstallationCommand` trait on the package service provider, in order to implement an installation command.
  - Needs the `installationCommand()` method applied within the [laravel-package-tools](https://github.com/spatie/laravel-package-tools) service provider's `bootingPackage()` method.

- **Configurated**
  - Ensures, as a package service provider's interface, that package configurations are validated and handled gracefully.
  - During `packageRegistered()`, you have to call `registerConfigurations()` method that's available in `ManagesConfigurations` trait.
  - It's used along `CraftyPackage::getConfiguration()` method.


### API

Below are the tables of all the `Crafty` package helpers:

<br/>

| **Interface**          | Description                                                                               |
|----------------|-------------------------------------------------------------------------------------------|
| `Configurated`  | Ensures that the package service provider has what's needed to deal with configurations [internally](src/CraftyServiceProvider.php#26). It Makes them compatible to be used in other packages and tests! Check out [ManagesConfigurations](src/Utilities/Configurated/Traits/ManagesConfigurations.php) trait.    |
| `Installable`  | Ensures that the package service provider has what's needed for its installation command. Check out [HasInstallationCommand](src/Utilities/Installable/Traits/HasInstallationCommand.php) trait.   |

<br/>

| **Trait**          | Description                                                                               |
|----------------|-------------------------------------------------------------------------------------------|
| `Enumerified`  | Extends enum functionality to retrieve counts, random instances, and enum collections.    |
| `HasInstallationCommand`  | Makes `installationCommand()` method available to be used in `bootingPackage()` method in order to set up an installation command.    |
| `ManagesConfigurations`  | Registers configurations and their validations for [CraftyPackage](src/CraftyPackage.php) to be able to handle them whenever they're needed.    |

<br/>

| **Laravel Rule**         | Description                                                                                               |
|--------------|-----------------------------------------------------------------------------------------------------------|
| `EnumsArray` | A validation rule that ensures an attribute is a filled array of valid enum values from a specified class.|

<br/>

| **Enumerified Function** | Description |
|--------------------------|-------------|
| `count(): int` | Returns the count of the enum cases. |
| `first(): self` | Returns the first enum case. |
| `random(int $amount = 1, self\|array $exceptFor = [], bool $asArray = false, bool $translated = false): self\|array` | Returns a random enum case or an array of random enum cases, with options to exclude certain cases; possible array results, and translated case names. |
| `names(self\|array $exceptFor = [], bool $translated = false): array` | Returns an array of enum case names, with options to exclude certain cases and to get the names traslated. |
| `values(self\|array $exceptFor = [], bool $asString = false): array\|string` | Returns an array of enum case values, with an option to exclude certain cases and to return values as a comma-separated string. |
| `collection(self\|array $exceptFor = [], bool $translated = true): Collection` | Returns a Laravel collection of enum case names and values, with options to exclude certain cases and to translate case names. |

<br/>

| **Crafty Facade Method**                                                              | Description                                                                                             |
|---------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| `chunkedDatabaseInsertion(string $tableName, array $dataArrays, ?Closure $callback = null): void` | Handles database insertions in chunks with configurable chunk size and default properties.             |
| `filterProps(mixed $all, mixed $only = [], mixed $except = []): array` | Capable of filtering for the common "only" and "except" criteria when dealing with a collection or an array (associative supportive too).             |
| `uniquelyMetaHashSuffixed(string $string): string`                          | Appends a unique hash suffix to a string. Utilizes the global helper function `unique_meta_hashing_number` of this same package.                                                               |
| `reverseString(string $string, EncodingType $encoding = EncodingType::UTF8): string` | Reverses a string according to the specified encoding type.                                             |
| `validatedArray(array $array, ValidatedDataType $keysOrValuesType, Closure\|ValidatedDataType\|null $valuesTypeOrNestedValidator = null): bool` | A decent way of validating arrays and associative arrays real quick. Check out the [CraftyTest.php](./tests/CraftyTest.php#L45) for examples.                                             |

<br/>

| **CraftyPackage Facade Method**                                                              | Description                                                                                             |
|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| `getConfiguration(string $key, $default = null): mixed`                                             | Returns the package configuration value gracefully **after validation**. Still, you **must** implement [Configurated](src/Utilities/Configurated/Interfaces/Configurated.php) interface and [ManagesConfigurations](src/Utilities/Configurated/Traits/ManagesConfigurations.php) methods for it to work. |
| `setConfiguration(string $key, mixed $value):  void`                                                | Sets a configuration value for a specified key. This method does not validate the value itself; use `setConfigurationValidation` to establish value constraints. Requires Configurated interface too. |
| `setConfigurationValidation(string $key, callable $closure): void`                                 | Registers a validation function for a specific configuration key. The function is called to validate the value each time it is set using `setConfiguration`. Requires Configurated interface too. |
| `seed(string $serviceProviderNamespace, string $seederName): void`                                 | Runs a package seeder by deriving the class out of Installable's `seederFilePaths()` in order to `run()` it manually. |

<br/>

| **Global Function**              | Description                                                                |
|-----------------------|----------------------------------------------------------------------------|
| `is_filled_string($value): bool`| Checks quickly for the most common validation EVER! A filled string.                    |
| `is_associative_array($array): bool`| Checks if the passed array is an associative one.                    |
| `is_enum(mixed $enum): bool`| Checks if the provided value is an instance of an enum.                    |
| `unique_meta_hashing_number(string $string, ?int $digits = null): string` | Generates a unique hash number based on the input string and optional digit limit. **Not for security purposes**, merely for general meta information tagging. |

<br/>

### Package Development

To integrate this package into the development of another package, ensure you load it first within your [TestCase](./tests/TestCase.php) file:

```php
class TestCase extends Orchestra
{
    // ...
    public function ignorePackageDiscoveriesFrom()
    {
        return [
            'vpremiss/arabicable', // the other package
            'vpremiss/crafty',
        ];
    }
    
    protected function getPackageProviders($_)
    {
        return [
            \VPremiss\Crafty\CraftyServiceProvider::class,
            \VPremiss\Arabicable\ArabicableServiceProvider::class, // the other package
        ];
    }
    // ...
}
```

### Changelogs

You can check out the package's [changelogs](https://app.whatthediff.ai/changelog/github/VPremiss/Crafty) online via WTD.

### Progress

You can also checkout the project's [roadmap](https://github.com/orgs/VPremiss/projects/6) among others in the organization's dedicated section for [projects](https://github.com/orgs/VPremiss/projects).


## Support

Support ongoing package maintenance as well as the development of **other projects** through [sponsorship](https://github.com/sponsors/VPremiss) or one-time [donations](https://github.com/sponsors/VPremiss?frequency=one-time&sponsor=VPremiss) if you prefer.

And may Allah accept your strive; aameen.

### License

This package is open-sourced software licensed under the [MIT license](LICENSE.md).

### Credits

- [ChatGPT](https://chat.openai.com)
- [Graphite](https://graphite.dev)
- [Laravel](https://github.com/Laravel)
- [Spatie](https://github.com/spatie)
- [BeyondCode](https://beyondco.de)
- [The Contributors](../../contributors)
- All the [backend packages](./composer.json#20) and services this package relies on...
- And the generous individuals that we've learned from and been supported by throughout our journey...


<div align="center">
   <br>والحمد لله رب العالمين
</div>

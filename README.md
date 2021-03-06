LcnWeatherForecastBundle
========================

Easy Forecast.io weather forecast Symfony 2 integration. MIT License.

Installation
------------

### Step 1: Download the Bundle

Open a command console, enter your project directory and execute the
following command to download the latest stable version of this bundle:

```bash
$ composer require locaine/lcn-weather-forecast-bundle "~1.0"
```

This command requires you to have Composer installed globally, as explained
in the [installation chapter](https://getcomposer.org/doc/00-intro.md)
of the Composer documentation.

### Step 2: Enable the Bundle

Then, enable the bundle by adding the following line in the `app/AppKernel.php`
file of your project:

```php
<?php
// app/AppKernel.php

// ...
class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            // ...

            new Lcn\WeatherForecastBundle\LcnWeatherForecastBundle(),
        );

        // ...
    }

    // ...
}
```


### Step 3: Setup Forecast.io API token

Paste your free [Forecast.io API token](https://developer.forecast.io/) in app/config/parameters.yml:

```yaml
parameters:
    ...
    lcn.weather_forecast_provider.forecast_io.api_token: YOUR_TOKEN
    ...

### Step 4: Adjust caching (optional)

The default cache engine caches the api results in files within the %kernel.cache_dir% directory.
You can, however, provide any Cache Provider implementing the [Doctrine Cache](https://github.com/doctrine/DoctrineCacheBundle) interface:

In config.yml or services.yml:


```yaml
services:
...
    lcn.weather_forecast.cache:
        class: Doctrine\Common\Cache\PhpFileCache
        arguments: [%kernel.cache_dir%]
        calls:
            - [setNamespace, ['lcn_weather_forecast']]
...
```


Usage
-----

Example controller code:

```php
$forecast = $this->container->get('lcn.weather_forecast');

$lat = 53.553521;
$lng = 9.948773;


$forecastForDay = $forecast->getForToday($lat, $lng);
// OR:
$forecastForDay = $forecast->getForDay($lat, $lng, strtotime('tomorrow'));

echo $forecastForDay->getSummary();
echo $forecastForDay->getIcon();
foreach ($forecastForDay->getHours() as $hour => $forecastForHour) {
    echo $forecastForHour->getSummary();
    echo $forecastForHour->getIcon();
    $key = 'temperature'; // $key can be any key from forecast.io api, e.g. time, summary, icon, precipIntensity, precipIntensity, precipProbability, temperature, apparentTemperature, dewPoint, humidity, windSpeed, windBearing, visibility, cloudCover, pressure, ozone
    echo $forecastForHour->get($key, 'fallback default value');
}

$forecastForHour = $forecast->getForCurrentHour($lat, $lng);
// OR:
$forecastForHour = $forecast->getForHour($lat, $lng, strtotime('+ 1 day 3 hours'));

echo $forecastForHour->getSummary();
echo $forecastForHour->getIcon();
$key = 'temperature'; // $key can be any key from forecast.io api, e.g. time, summary, icon, precipIntensity, precipIntensity, precipProbability, temperature, apparentTemperature, dewPoint, humidity, windSpeed, windBearing, visibility, cloudCover, pressure, ozone
echo $forecastForHour->get($key, 'fallback default value');

```

# FeatureFlagBundle

[![Build Status](https://travis-ci.org/novaway/NovawayFeatureFlagBundle.svg?branch=master)](https://travis-ci.org/novaway/NovawayFeatureFlagBundle?branch=master)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/novaway/NovawayFeatureFlagBundle/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/novaway/NovawayFeatureFlagBundle/?branch=master)
[![Latest Stable Version](https://poser.pugx.org/novaway/feature-flag-bundle/v/stable.png)](https://packagist.org/packages/novaway/feature-flag-bundle)

The FeatureFlagBundle is a very KISS bundle to manage features flags in your Symfony
applications.

Just allow to know if a feature is enable or not. If you have more complex needs,
please considerer using an another solution like [Qandidate\Toggle](https://github.com/qandidate-labs/qandidate-toggle-bundle).

## Compatibility

This bundle is tested with all maintained Symfony version, but it should be compatible with
Symfony 2.3+.

## Documentation

### Install it

Install extension using [composer](https://getcomposer.org):

```bash
composer require novaway/feature-flag-bundle
```

If you don't use Flex, enable the bundle in your application `AppKernel`:

```php
<?php

public function registerBundles()
{
    $bundles = [
        // ...
        new Novaway\Bundle\FeatureFlagBundle\NovawayFeatureFlagBundle(),
    ];

    // ...

    return $bundles;
}
```

### Use it

#### Define your features

First you have to configure the bundle and define your feature flags in the `config.yml`.

```yaml
# ...
novaway_feature_flag:
    features:
        my_feature_1: false
        my_feature_2: true
```

#### As a service

The bundle add a `novaway_feature_flag.manager.feature` service you can use in your
PHP classes.

```php
class MyController extends Controller
{
    public function myAction()
    {
        $featureManager = $this->get('novaway_feature_flag.manager.feature');
        
        if ($featureManager->isEnabled('my_feature_1')) {
            // my_feature_1 is enabled
        }
        
        if ($featureManager->isDisabled('my_feature_2')) {
            // my_feature_2 is not enabled
        }
    }
}
```

#### In your Twig templates

You can also check a flag in your templates:

```twig
{% if isFeatureEnabled('my_feature_1') %}
    {% include 'feature1_template.html.twig' %}
{% endif %}

{% if isFeatureDisabled('my_feature_2') %}
    {% include 'feature2_template.html.twig' %}
{% endif %}
```

#### In the routing configuration

The package allow you to restrict a controller access by adding some configuration in your routing definition.

```yaml
# app/config/routing.yml
my_first_route:
    path: /my/first/route
    defaults:
        _controller: AppBundle:Default:index
        _features:
            - { feature: my_feature_key, enabled: false } # The action is accessible if "my_feature_key" is disabled
            
my_second_route:
    path: /my/second-route
    defaults:
        _controller: AppBundle:Default:second
        _features:
            - { feature: foo } # The action is accessible if "foo" is enabled ...
            - { feature: bar, enabled: true } # ... and "bar" feature is also enabled
```

#### As a controller annotation

You can also restrict a controller access with annotations :

```php
class MyController extends Controller
{
    /**
     * @Feature("foo")
     */
    public function annotationFooEnabledAction()
    {
        return new Response('MyController::annotationFooEnabledAction');
    }
    
    /**
     * @Feature("foo", enabled=true)
     */
    public function annotationFooEnabledBisAction()
    {
        return new Response('MyController::annotationFooEnabledAction');
    }

    /**
     * @Feature("foo", enabled=false)
     */
    public function annotationFooDisabledAction()
    {
        return new Response('MyController::annotationFooDisabledAction');
    }
}
```

### Implement your own storage provider

1. First your need to create your storage provider class which implement the `Novaway\Bundle\FeatureFlagBundle\Storage\StorageInterface` interface
2. Declare your storage provider as a service

## License

This library is published under [MIT license](LICENSE)

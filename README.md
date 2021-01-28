# Pimcore E-Commerce Framework Payment Provider - OGone

### Official OGone Documentation
* [Documentation](https://payment-services.ingenico.com/int/en/ogone/support/guides/integration%20guides/e-commerce/introduction)
* [Sandbox](https://payment-services.ingenico.com/int/en/ogone/support/guides/user%20guides/test-account-creation) / on request

## Installation

Install latest version with Composer:
```bash 
composer require pimcore/payment-provider-ogone
```

Enable bundle via console or extensions manager in Pimcore backend:
```bash
php bin/console pimcore:bundle:enable PimcorePaymentProviderOGoneBundle
php bin/console pimcore:bundle:install PimcorePaymentProviderOGoneBundle
```

## Configuration
The Payment Manager is responsible for implementation
of different Payment Provider to integrate them into the framework. 

For more information about Payment Manager, see 
[Payment Manager Docs](../13_Checkout_Manager/07_Integrating_Payment.md). 

Configure payment provider in the `pimcore_ecommerce_config.payment_manager` config section: 
```yaml
pimcore_ecommerce_config:
    payment_manager:
        # service ID of payment manager implementation - following value is default value an can be omitted
        payment_manager_id: Pimcore\Bundle\EcommerceFrameworkBundle\PaymentManager\PaymentManager

        # configuration of payment providers, key is name of provider
        providers:
            ogone:
                provider_id: Pimcore\Bundle\EcommerceFrameworkBundle\PaymentManager\Payment\OGone
                profile: sandbox
                profiles:
                    sandbox:
                        secret: D343DDFD3434
                        pspid: MyTestAccount
                        mode: sandbox                        
#                       encryptionType: SHA256 or SHA512 (optional)                                              
                    live:
                        secret: D343DDFD3434
                        pspid: MyLiveAccount
                        mode: live                        
#                       encryptionType: SHA256 or SHA512 (optional)
```

## Implementation

Somewhere in your checkout controller you will need to create the payment configuration for the `initPayment()`
method of the provider:

```php
<?php
    $url = 'https://'. $_SERVER["HTTP_HOST"] . "/en/checkout/confirm?state=";
    $paymentConfig = [                   
                    'language'            => "en",
                    'orderIdent'          => $paymentInfo->getInternalPaymentId(),                   
                    'customerStatement'   => $paymentMessage,
                    'successUrl'          => "https://my-server-name.com/shop/payment/confirm?provider=ogone&state=success",
                    'cancelUrl'           => "https://my-server-name.com/shop/payment/confirm?provider=ogone&state=cancel",
                    'errorUrl'            => "https://my-server-name.com/shop/payment/confirm?provider=ogone&state=error",
                    'paymentInfo'         => $order->getPaymentInfo()->getItems()[0]
                ];
```

You must configure the callback URLs within the OGone backend so that these are called server-by-server.

You can pass additional parameters in the configuration based on the OGone documentation. For instance the color and 
the appearance of th Ogone UI can be controlled, and additional customer data may be passed.
# xom

TYPO3-Extension providing service classes to work with the xom REST API. More information about xom can be found here: https://www.myview.de/xom-api

## Installation

Just add this extension using ```composer require medienreaktor/xom``` and make sure to configure the installation-wide extension configuration:

 * ```apiEndpoint``` – REST API Endpoint,  e.g. https://company.xom.one/xom-rest/
 * ```apiOAuthEndpoint``` – OAuth Token Endpoint, e.g. https://company.xom.one/xom-daemon/oauth/token
 * ```apiClientId``` and ```apiClientSecret``` with your credentials

## Features and usage

The ```XomService```-class includes

 * Authentication
 * Caching
 * Localization

for the xom REST API. Use it in your own extension by extending the ```XomService```-class and adding your API calls.

### Example

Add your own service-class for fetching product data:

```php
<?php
namespace YourVendor\XomProducts\Service;

class XomProductsService extends \Medienreaktor\Xom\Service\XomService
{
    const REQUEST_PRODUCTS = 'products';
    const REQUEST_PRODUCTS_ID = 'products/{id}';

    public function listProducts() {
        return $this->apiCall(self::REQUEST_PRODUCTS);
    }

    public function findProductById($identifier) {
        return $this->apiCall(self::REQUEST_PRODUCTS_ID, $identifier);
    }
}
```

Caching of requests and responses will be done automatically. For better caching control, consider implementing additional caching yourself.

If you want to opt-out of the default caching, see the parameters of the ```apiCall```-method:

| Parameter   | Type    | Description                | Default |
|-------------|---------|----------------------------|---------|
| $uri        | string  | URI for API call, required |         |
| $identifier | string  | Node identifier, optional  | NULL    |
| $params     | array   | Request parameters         | []      |
| $localized  | boolean | Localize responses?        | TRUE    |
| $cached     | boolean | Cache responses?           | TRUE    |
| $method     | string  | HTTP method                | 'GET'   |

In your controller, inject your service-class, perform your calls and assign the results to the view:

```php
<?php
namespace YourVendor\XomProducts\Controller;

class ProductController extends \TYPO3\CMS\Extbase\Mvc\Controller\ActionController
{
    /**
     * xomProductsService
     *
     * @var \YourVendor\XomProducts\Service\XomProductsService
     * @TYPO3\CMS\Extbase\Annotation\Inject
     */
    protected $xomProductsService = NULL;

    /**
     * action list
     *
     * @return void
     */
    public function listAction() {
        $products = $this->xomProductsService->listProducts();
        $this->view->assign('products', $products);
    }
}
```

## Support

This extension is provided without warranty of any kind and released under the GPL-3 license. For professional support and development, please contact us: https://www.medienreaktor.de

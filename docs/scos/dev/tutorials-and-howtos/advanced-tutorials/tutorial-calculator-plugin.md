---
title: Tutorial - Calculator plugin
description: Use the guide to create and register a calculator plugin to the calculator plugin stack.
last_updated: Jun 16, 2021
template: howto-guide-template
originalLink: https://documentation.spryker.com/2021080/docs/t-calculator-plugin
originalArticleId: d0f57a1d-9914-4a39-9cff-1796c26a27cb
redirect_from:
  - /2021080/docs/t-calculator-plugin
  - /2021080/docs/en/t-calculator-plugin
  - /docs/t-calculator-plugin
  - /docs/en/t-calculator-plugin
  - /v6/docs/t-calculator-plugin
  - /v6/docs/en/t-calculator-plugin
  - /v5/docs/t-calculator-plugin
  - /v5/docs/en/t-calculator-plugin
  - /v4/docs/t-calculator-plugin
  - /v4/docs/en/t-calculator-plugin
  - /v3/docs/t-calculator-plugin
  - /v3/docs/en/t-calculator-plugin
  - /v2/docs/t-calculator-plugin
  - /v2/docs/en/t-calculator-plugin
  - /v1/docs/t-calculator-plugin
  - /v1/docs/en/t-calculator-plugin
---

<!-- used to be: http://spryker.github.io/tutorials/zed/calculator-plugin/
-->

This tutorial explains how to add a new calculation plugin to the calculator stack.

Requirement: display the tax amount per item.

Right now, you can get the tax amount from `grandTotal`. For this, you have to add a new calculator to the existing stack for the module. To do that:

1. First, there are some data structure changes that you need to make. Modify the `ItemTransfer` object by adding two new properties :

* `unitTaxAmount` for a single item
* `sumTaxAmount` tax amount for the sum of items

2. As this is tax-related, you have to add it on a project level in the `Tax` module.

Modify the `tax.transfer.xml` transfer object to reflect the new data model. Add the following changes to the `Pyz/Shared/Tax/Transfer/tax.transfer.xml` file:

```xml
<transfer name="Item">
     <property name="unitTaxAmount" type="int" />
     <property name="sumTaxAmount" type="int" />
 </transfer>
```

3. Run the following console command:

```bash
vendor/bin/console transfer:generate
```

Once done, two new properties appear in the `ItemTransfer`.

4. Next, create a new calculator plugin and register it to the calculator plugin stack.

In the `Pyz/Zed/Tax` namespace, create a new module if it does not exist. Then, create a new plugin class under `Pyz/Zed/Tax/Communication/Plugin/ItemTaxAmountCalculatorPlugin`, as you see in the example below:

```php
<?php
namespace Pyz\Zed\Tax\Communication\Plugin;

use Generated\Shared\Transfer\CalculableObjectTransfer;
use Spryker\Zed\Kernel\Communication\AbstractPlugin;
use Spryker\Zed\CalculationExtension\Dependency\Plugin\CalculationPluginInterface;

/**
 * @method \Spryker\Zed\Tax\Business\TaxFacade getFacade()
 */
class ItemTaxAmountCalculatorPlugin extends AbstractPlugin implements CalculationPluginInterface
{
    /**
     * @param \Generated\Shared\Transfer\CalculableObjectTransfer $calculableObjectTransfer
     *
     * @return void
     */
    public function recalculate(CalculableObjectTransfer $calculableObjectTransfer)
    {
        $this->getFacade()->calculateItemTax($calculableObjectTransfer);
    }
}
```

5. Add a new plugin to the calculator stack `Pyz\Zed\Calculation\CalculationDependencyProvider::getQuoteCalculatorPluginStack()`:

```php
<?php
   protected function getQuoteCalculatorPluginStack(Container $container)
   {
       return [
           // ... other plugins add this to place where required amounts are already calculated, for example after ItemCalculator.
           new ItemTaxAmountCalculatorPlugin(),
       ];
   }
```

6. Add a new facade method: `Pyz\Zed\Tax\TaxFacade::calculateItemTax()` and create the `TaxFacade` class extending Spryker Core `TaxFacade` if it is not present.

```php
<?php
    /**
      * @param \Generated\Shared\Transfer\CalculableObjectTransfer $calculableObjectTransfer
      *
      * @return void
      */
   public function calculateItemTax(CalculableObjectTransfer $calculableObjectTransfer)
   {
       $this->getFactory()->createItemTaxCalculator()->recalculate($calculableObjectTransfer);
   }
```

7. Create the `ItemTaxCalculator` that implements the tax calculation business logic. Place this class under `\Pyz\Zed\Calculation\Business\Model\ItemTaxCalculator`.

```php
<?php
namespace Pyz\Zed\Tax\Business\Model;

use Generated\Shared\Transfer\CalculableObjectTransfer;

class ItemTaxCalculator
{
    /**
     * @param \Generated\Shared\Transfer\CalculableObjectTransfer $calculableObjectTransfer
     *
     * @return void
     */
    public function recalculate(CalculableObjectTransfer $calculableObjectTransfer)
    {
        //tax calculator business logic
    }
}
```

8. Add a new factory method for the new calculator to `Pyz\Zed\Tax\Business\TaxBusinessFactory`. Create the factory class if it does not exist, extending the Spryker Core factory.

```php
<?php
/**
    * @return \Pyz\Zed\Tax\Business\Model\ItemTaxCalculator
    */
   protected function createItemTaxCalculator()
   {
       return new ItemTaxCalculator();
   }
```

That's it! The new calculator plugin is added to the calculator stack.
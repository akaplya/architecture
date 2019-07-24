

# Schema 

![Product Schema](product-schema/product-schema.png)

# Product options

The main difference between product templates (types)
that we have at the moment can be expressed through the 
options they provide.
With the current situation, we have as many product
options types as product types some of the options can be 
combined, which multiplies product template variations 
for a storefront.

So, current product schema looks like *
```json
  {
    "sku" : "some-product-sku",
    "customizableOptions": [{}, {}],
    "downloadableOptions": [{}, {}],
    "configurableOptions": [{}, {}],
    "bundleOptions": [{}, {}],
    "giftAmountOptions": [{}, {}] 
  }
```

The purpose of this document is to explain how to avoid option types multiplication for product storefront representation.


### Common options

Let's figure out options common part for all product types.
To display a list of options at the storefront is enough to know about:
* Option title.
* Set of variants that will be accessible at the storefront.
* Is this option mandatory.

Options and variants should to provide IDs to make it possible recognize them.

```json
{
  "commonOptions":
  [
    {
      "title": "Fabric",
      "is_required": true,
      "variants" : [
        {
          "title": "cotton"
        },
        {
          "title": "polyester"
        }
      ]
    }
  ]
}
```

```graphql
interface VariantInterface{
    variant_id: ID
	title : String!
}

interface OptionInterface{
    option_id: ID
	title : String!
	is_required : Boolean!
	variants : [VariantInterface!]!
}
```
This bare minimum that we need to display options at product page. 
In the same time, this is enough to build the simplest custom options which have business value.
Let's take it as the starting point.

### Option IDs 
Options have to expose ID. 

identify options on a page
perform lookups 
interact with other business processes like adding a product to cart.

Currently, Magento does not expose good natural identifiers for options and values.
So we need to introduce them.
As a desired state we want to use UUID.
We can not use database IDs:
* we do not want to use database IDs explicitly at the storefront [link on techical guidelines]
* we can not do this because different options stored in different tables.
To respect the actual implementation will can use base64encode of attributes which represent options/values in unique way.

### Options with price
Some of the options may require to specify a price adjustment for the original product price.
Because this is storefront representation, our schema may not rely on the way how the option price is defined.
It is enough to return the calculated values.
We do not care how the price was defined, as a constant adjustment or as a percent from the original price.
Actually, this is good from the unification perspective.
As a result, all price values are going to have a unified representation across the storefront.

```graphql
type Price {
    value : Money
    ajustments: [Adjustment!] 
}

interface PriceVariantInterface {
    variant_id: ID
 	price : Price
}
```

### Product as an Option
Some of the options represent a product or a product abstraction.
For such purposes, the option can be equipped with SKU as an extension.

```graphql
interface ProductVariantInterface {
    variant_id: ID
 	sku : String
}
```

### Example: GiftCard Product

Gift card product allows selecting from various amounts that can be assigned to it.

```graphql
type GiftCardOption implements OptionInterface{
    option_id: ID
	title : String!
	is_required : Boolean!
	variants : [GiftCartAmountVariant!]!
}

type GiftCartAmountVariant implements PriceVariantInterface
{
    variant_id: ID
	title : String!
	price: Price
}
```

### Option input types 
A merchant can specify an input type for options.
The input type explains how options will be displayed.
Also, the option input type says - is it possible to select several variants from one option.

```graphql
enum OptionInputTypes {
	DROP_DOWN
	RADIO_BUTTONS
	CHECKBOX
	MULTIPLE_SELECT
}

interface InputTypeInterface {
    option_id: ID
	input_type : OptionInputTypes
}
```

### Example: Simple product with Customizable Option.

Currently we have enough information to figure out declaration of a classic customizable option.
```graphql
type CustomizalbeOption implements OptionInterface, InputTypeInterface {
    option_id: ID
	title : String!
	is_required : Boolean!
	input_type : OptionInputTypes
	variants : [CustomizalbeVariant!]!
}

type CustomizalbeVariant implements PriceVariantInterface, ProductVariantInterface
{
    variant_id: ID
	title : String!
	price: Price
	sku: String
}
```

### Example: Bundle Product


### Example: Downloadable Product


### Example: Configurable Product



# Schema 

![Product Schema](product-schema/product-schema.png)

# Product options
Customizable options have been split into two independent groups: options provided by a merchant and options provided by a buyer.
As a result, we were able to achieve to goals: 
* removed nesting levels from options structure.
* merchant defined options become reusable.
So, we do not need more downloadable options and bundle options as separate attributes. 
bundle options = merchant options + inventory
downloadable options = merchant options + samples

# Product prices

## Price range instead of prices.

Listing page has to show price range for products.
minimal list price 
minimal sales price
maximal list price 
maximal sales price 

``` graphql

type Query{
	products : [Product]
	product_attributes : [ProductAttributeMetadata]
}

type Product{
	product_id : ID!
	scope : [Scope!]!
	sku : String!
	url_key : String!
	url : String!
	name : String!
	meta_description : String
	meta_keyword : String
	meta_title : String
	description : String
	short_description : String
	visibility : ProductVisibility!
	displayable : Boolean!
	buyable : Boolean!
	tax_class_id : String!
	weight : Float
	weight_unit : WeightUnit
	is_weight_dynamic : Boolean
	created_at : String
	updated_at : String
	type_id : ProductType
	qty : Float
	image : ProductImage
	small_image : ProductImage
	thumbnail : ProductImage
	media_gallery : [MediaEntryInterface!]
	attributes : [Attribute]
	price_range : PriceRange!
	variants : [ProductVariant!]
	options : [ProductOption!]
	buyer_input : [ProductDesign]
	categories : [String]
}

type Scope{
	code : String!
	value : String!
}

enum ProductVisibility{
	CATALOG
	SEARCH
	CATALOG_SEARCH
	NOT_VISIBLE_INDIVIDUALLY
}

enum WeightUnit{
	KILOGRAM
	GRAM
	POUND
}

enum ProductType{
	SIMPLE
	VIRTUAL
	CONFIGURABLE
}

type ProductImage implements MediaEntryInterface{
	url : String
	label : String
}

interface MediaEntryInterface{
	url : String
	label : String
}

type Attribute{
	code : String!
	value : String
}

type PriceRange{
	minimalPirce : ProductPrice!
	maximalPrice : ProductPrice
}

type ProductPrice{
	regularPrice : Price!
	salePrice : Price!
}

type Price{
	value : Money!
	ajustment : [Adjustment!]
}

type Money{
	amount : Float!
	currency : Currency!
}

enum Currency{
	USD
}

type Adjustment{
	value : Money!
	code : String!
}

type ProductVariant{
	option_identification : [AttributeSelection!]
	sku : String!
	price_range : PriceRange!
}

type AttributeSelection{
	code : String!
	value : String!
}

type ProductOption{
	title : String!
	is_required : Boolean!
	display_option : DisplayOption!
	variants : [ProductOptionVariant!]!
}

enum DisplayOption{
	DROP_DOWN
	RADIO_BUTTONS
	CHECKBOX
	MULTIPLE_SELECT
}

type ProductOptionVariant{
	title : String!
	sku : String!
	price : Money
}

type ProductDesign{
	title : String!
	is_required : Boolean!
	design_input : DesignInput!
	price : Money
}

enum DesignInput{
	FIELD
	AREA
	FILE
	DATE
}

type ProductAttributeMetadata{
	code : String!
	data_type : AttributeDataType!
	is_sortable : Boolean!
	is_visible : Boolean!
	is_searchable : Boolean!
	is_filterable : Boolean!
	is_comparable : Boolean!
	position : Int!
	search_weight : Float
	predefined_options : [String!]
}

enum AttributeDataType{
	STRING
	INT
	FLOAT
	CHOICE
}

enum ScopeCode{
	WEBSITE
	STORE
	CUSTOMER_GROUP
}

type ProductVideo implements MediaEntryInterface{
	url : String
	label : String
	title : String
	description : String
	provider : String
	metadata : String
}
schema{
	query: Query
}

```

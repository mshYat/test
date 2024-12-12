All TWIG code must be enclosed in ```{% verbatim %}```. Example:
```twig
{% verbatim %}
  {{ itemContent.variation.id }}
{% endverbatim %}
```

# Fields

```{{ itemContent.item.id }}``` - Article ID

```{{ itemContent.variation.id }}``` - Item ID

```{{ itemContent.variation.number }}``` - Item number

```{{ itemContent.texts.description | raw }}``` - Article description

```{{ itemContent.texts.shortDescription | raw }}``` - Article short description

```{{ itemContent.texts.technicalData | raw }}``` - Article technical description

```{{ itemContent.texts.name1 }}``` - Name 1

```{{ itemContent.texts.name2 }}``` - Name 2

```{{ itemContent.texts.name3 }}``` - Name 3

```{{ itemContent.texts.description | raw }}``` - Article description

```{{ itemContent.freeFields.free1 }}``` - Free field( range: free1 - free20 )

```{{ itemContent.item.manufacturer.externalName }}``` - Manufacturer name

```{{ itemContent.item.manufacturer.logo }}``` - Manufacturer logo

```{{ itemContent.prices.default.price.formatted }}``` - Regular price


## Advanced

[Twig Documentation](https://twig.symfony.com/doc/2.x/)

Use */artikel_pdf/twig/item/{itemId}/variation/{variationId}* route to see all available fields in comment section ```<!-- itemContent json: {fields} -->```

## Examples

### Current date
```twig
<div class="date-now">
  {{ 'now'|date('Y-m-d H:i:s') }}
</div>
```

### Availability
```twig
{% if itemContent.variation.availability %}
  <div class="property availability small">
    <b>{{ itemContent.variation.availability.names.name }}</b>
  </div>
{% endif %}
```

### Article Name
```twig
{% import "Ceres::PageDesign.Macros.ItemName" as ItemName %}
<h2>{{ ItemName.get(itemContent | itemName, itemContent.variation.bundleType) }}</h2>
```

### Article Url
```twig
{% set itemUrl = services.url.getVariationURL(itemContent.item.id, itemContent.variation.id).toAbsoluteUrl(true) %}
<a class="product-link" href="{{ itemUrl }}" target="_blank">{{ itemUrl }}</a>
```

### Article Image
```twig
{% set image = itemContent.images.all[0].url %}
{% if itemContent.images.variation is not empty and itemContent.images.variation[0].url is not empty %}
  {% set image = itemContent.images.variation[0].url %}
{% endif %}
<div class="product-image-container">
  <img class="product-image" src="{{ image }}" style="max-width:100%;">
</div>
```

### Price
```twig
<div class="price">
  <span class="regular-price">{{ itemContent.prices.default.price.formatted }}</span>
  {% if itemContent.prices.rrp and itemContent.prices.rrp.unitPrice.value > 0 and itemContent.prices.rrp.unitPrice.value > itemContent.prices.default.unitPrice.value %}
    <del class="crossprice">{{ itemContent.prices.rrp.unitPrice.formatted }}</del>
  {% endif %}
</div>
```

### Manufacturer Name
```twig
{% if itemContent.item.manufacturer.externalName is not empty and itemContent.filter.hasManufacturer %}
<div class="producertag">
  {{ itemContent.item.manufacturer.externalName }}
</div>
{% endif %}
```

### Graduated Price
```twig
{% set minQuantity = itemContent.variation.minimumOrderQuantity %}
{% set graduatedPrices = itemContent.prices.graduatedPrices %}
{% set graduatedPrices =  graduatedPrices | orderByKey ('minimumOrderQuantity') %}
{% if graduatedPrices and graduatedPrices[0] %}
  <div class="graduated-prices mb-15">
    <b>{{ trans("Ceres::Template.singleItemGraduatedPrices") }}:</b>
    <table class="graduated-prices-table">
      {% for price in graduatedPrices %}
        {% if price.minimumOrderQuantity > minQuantity %}
          <tr>
            <td>{{ trans("Ceres::Template.singleItemMinimumQuantity") }} {{ price.minimumOrderQuantity }}</td>
            <td>{{ price.unitPrice.formatted }}</td>
          </tr>
        {% endif %}
      {% endfor %}
    </table>
  </div>
{% endif %}
```

### Unit
```twig
{% if itemContent.unit %}
  <div class="unit">
  {{ trans("Ceres::Template.singleItemContent") }} {{ itemContent.unit.content }} {{ itemContent.unit.names.name }}
  </div>
{% endif %}
```

### Vat
```twig
<div class="vat">
  {% if services.customer.showNetPrices() %}
    {{ trans("Ceres::Template.singleItemExclVAT") }}
  {% else %}
    {{ trans("Ceres::Template.singleItemInclVAT") }}
  {% endif %} {{ trans("Ceres::Template.singleItemExclusive") }} {{ trans("Ceres::Template.singleItemShippingCosts") }}
</div>
```

### Order Attributes
```twig
{% if itemContent.attributes | length > 0 %}
  <div class="attributes">
    {% for attribute in itemContent.attributes %}
      <div class="attribute">
        <b>{{ attribute.attribute.names.name }}: </b>{{ attribute.value.names.name }}
      </div>
    {% endfor %}
  </div>
{% endif %}
```

### Properties Loop
```twig
{% if itemContent.properties | length > 0 %}
  <div class="properties">
    {% for property in itemContent.properties %}
      {% if property.texts.value | length > 0 %}
        <div class="property">
          <b>{{ property.property.names.name }}: </b>{{ property.texts.value | raw }}
        </div>
        {% endif %}
    {% endfor %}
  </div>
{% endif %}
```

### Properties By Id
```twig
{% for property in itemContent.properties %}
  {% if property.property.id in [15, 17] and property.property.names.name | length > 0 and property.texts.value | length > 0 %}
    <div class="property-15-and-17">
      <b>{{ property.property.names.name }}: </b>{{ property.texts.value | raw }}
    </div>
  {% endif %}
{% endfor %}
```

### Variation Properties Loop
```twig
{% if itemContent.variationProperties | length > 0 %}
  <div class="variation-properties">
    {% for propertyGroup in itemContent.variationProperties %}
      {% for property in propertyGroup.properties %}
        {% if property.names.name | length > 0 and property.values.value | length > 0 %}
          <div class="v-prop" data-id="{{ property.id }}">
            <b>{{ property.names.name }}: </b>{{ property.values.value | raw }}
          </div>
        {% endif %}
      {% endfor %}
    {% endfor %}
  </div>
{% endif %}
```

### Variation Properties By Id
```twig
{% if itemContent.variationProperties | length > 0 %}
  {% for propertyGroup in itemContent.variationProperties %}
    {% for property in propertyGroup.properties %}
      {% if property.id in [15, 17] and property.names.name | length > 0 and property.values.value | length > 0 %}
        <div class="v-prop" data-id="{{ property.id }}">
          <b>{{ property.names.name }}: </b>{{ property.values.value | raw }}
        </div>
      {% endif %}
    {% endfor %}
  {% endfor %}
{% endif %}
```

### Variation Properties By Group Id
```twig
{% if itemContent.variationProperties | length > 0 %}
  {% for propertyGroup in itemContent.variationProperties %}
    {% for property in propertyGroup.properties %}
      {% if propertyGroup.id == 3 and property.names.name | length > 0 and property.values.value | length > 0 %}
        <div class="v-prop" data-id="{{ property.id }}">
          <b>{{ property.names.name }}: </b>{{ property.values.value | raw }}
        </div>
      {% endif %}
    {% endfor %}
  {% endfor %}
{% endif %}
```

### Free field condition
```twig
{% if freeFields.free9 | trim is not empty and freeFields.free9 != '0' %}
  <div class="freeField9">{{ freeFields.free9 | raw }}</div>
{% endif %}
```

### Barcodes loop
```twig
{% if itemContent.barcodes | length > 0 %}
  <div class="barcodes">
    {% for barcode in itemContent.barcodes %}
      {% set type = barcode.type %}
      {% if type == 'GTIN_13' %}
        {% set type = 'EAN13' %}
      {% elseif type == 'CODE_128' %}
        {% set type = 'C128B' %}
      {% elseif type == 'GTIN_128' %}
        {% set type = 'EAN128B' %}
      {% elseif type == 'UPC' %}
        {% set type = 'UPCA' %}
      {% elseif type == 'ISBN' %}
        {% set type = 'ISBN' %}
      {% elseif type == 'GTIN_8' %}
        {% set type = 'EAN8' %}
      {% elseif type == 'QR' %}
        {% set type = 'QR' %}
      {% else %}
        {% set type = 'NONSUPPORTED' %}
      {% endif %}
      {% if type != 'NONSUPPORTED' %}
        <div class="barcode">
          <barcode code="{{ barcode.code }}" type="{{ type }}" />
        </div>
      {% endif %}
    {% endfor %}
  </div>
{% endif %}
```

### MPDF tags examples [Documentation](https://mpdf.github.io/)

#### Header
```html
<htmlpageheader name="Header1">
  Header Content
</htmlpageheader>
```

#### Footer
```html
<htmlpagefooter name="Footer1">
  Footer Content
</htmlpagefooter>
```

#### Page break
```html
<pagebreak />
```

#### Barcode
```html
<barcode code="978020137962" type="EAN13" />
<barcode code="https://google.com" type="QR" />
```
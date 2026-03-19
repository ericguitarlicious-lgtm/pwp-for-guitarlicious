# PWP Custom Snippet for Shopify

## How It Works
Shopify admin panel already have a discount section, that we can set discount rules and promo codes.. 

But it has a limitation: It doesnt have any available templates or frontend stuffs that we can just drag and show to our online customers.

Therefore, this custom feature will be the "bridge" of existing shopify feature to get our job done, by designing a new PWP section in product detail page, and linking it with Shopify features such as Discount, Tags and Collection.

<img width="1152" height="437" alt="image" src="https://github.com/user-attachments/assets/43a75ccc-cc81-4967-aa28-4c276e0756fa" />

<br><br>

## Visual Summary of All Steps
<img width="1246" height="515" alt="image" src="https://github.com/user-attachments/assets/d984a1ec-aaaf-4afe-b687-dced486f8c49" />


<br><br>

## 1. Render Code to Show The PWP Section

For normal editing, you don't have to do this, it is the custom code to be added to the webpage on new product templates or new PWP discount rule.

Below is the liquid render code to show the section in product detail page template, it can be inserted into.

The values of `discount_value` and `contain_tag` need to be altered according to situation.

<br>

For Shopify drag-and-drop insert Custom Code feature:
```liquid
{% render '_pwp-collection-list-2', discount_values: "15,20,25,30", contain_tag: 'electric guitar' %}
```

For adding through manual code edit:
```liquid
<div class="page-width">
    {% render '_pwp-collection-list-2', discount_values: "15,20,25,30", contain_tag: 'electric guitar' %}
</div>
```

<br>

Input fields: 
- `discount_value` : enter a list of discount values (without %), separated by commas.

- `contain_tag` : enter a list of discount values, separated by commas.

    - Existing "contain_tag" :

        - `electric guitar` - For all electric guitars.
        - `classical guitar` - For all classical guitars.
        - `acoustic guitar` - For all acoustic guitars.
        - `bass guitar` - For all bass guitars.
        - `ukulele` - For all ukuleles.
        - `digital and acoustic drum` - For digital and acoustic drum
        - `keyboard and piano` - For all keyboard and piano.
        - `dj` - For all DJ related devices & equipments.

<br><br>

## 2. Updating & Auto Generating the Render Codes & Tags
You may refer to this Google Sheet to auto generate all the required code and tags.

[Google Sheet - PWP Shopify Product Listing (Need to Request for Access)](https://docs.google.com/spreadsheets/d/13Y-FQ35LbT1wzt44DpB7TNYZkHFwryaDFuoiElt2qJM/edit?usp=sharing)

<br>

### Steps 0 :

Enter the product category and discount value. You only need to type in column A and column C in `PWP Create New: Input Here`.

For example, `electric guitar` and `10` for 10% discount for add on products applied to electric guitars.

*** This is the only step you need to type on the Google Sheet, don't type anything after this to avoid overiding the formulas.
*** After this step, go to the next tab `PWP Create New: Output (JUST COPY, DO NOT EDIT)`

<br>

### Step 1 :
Main product here refer to the main product which offer pwp promotions on other addons products. Add 1 tag to link the Main Product (s)
 
``` pwp-enable ```

<br>

### STEP 2 :

Tag to link Addon Products, need to add 2 Tags for Addon Product.
`pwp-{discount value}`   `pwp-{product category}`

Example : 

``` pwp-10 ``` For example, pwp-10 for 10% discount.

``` pwp-10-electric guitar ```  For example, pwp-electric guitar for the addons of electric guitar.

<br><br>

.... Step 3 Onwards is ONLY for new pwp setting, you only need to set up one time. 

<br><br>

### STEP 3 : 

Ensure You Have the 2 required Product Collection Created, with the name as follows

``` pwp-{discount value}-{category} ``` - This collection will be selected while setting up Shopify 'Discount' feature.

``` pwp-{discount value} ``` - This is required to make the custom code works.

Example : 

1. ``` pwp-10-electric guitar ``` 
For discount, set the product tag automatically applies to `pwp-10` and `pwp-electric guitar`

2. ``` pwp-10 ```
For render, only need to add if theres a new percentage we've never use before.

<br>

### STEP 4 : 

Add Custom Code on Website Template(Place at Custom code in Shopify Editor or Custom Liquid File)"

Example : 

``` pwp-electric guitar ```

<br><br><br><br>
---
<br><br><br><br>

# Reference : Full Code (Backup)

This new created snippet file is located in \snippets\_pwp-collection-list.liquid (Just for reference, you may skip this part)

```liquid
{% comment %}
  PWP Horizontal Carousel - Updated Filename: _pwp-collection-list.liquid
  
  Logic:
  1. Main Product (PDP) must have tag: 'pwp-enable'
  2. Add-on Product must have tag: 'pwp-{{ discount_value }}'
  3. Add-on Product must have tag: 'pwp-{{ contain_tag }}'
{% endcomment %}

{% assign pwp_collection = collections[collection_handle] %}

{% comment %} Step 1: Check the Main Product (current page) {% endcomment %}
{% if product.tags contains 'pwp-enable' and pwp_collection and pwp_collection.products.size > 0 %}
  
  {% assign discount_num = discount_value | plus: 0 %}
  {% assign multiplier = 100 | minus: discount_num | divided_by: 100.0 %}
  
  {% comment %} Construct the required filter tags {% endcomment %}
  {% capture discount_tag %}pwp-{{ discount_num }}{% endcapture %}
  {% capture category_tag %}pwp-{{ contain_tag | downcase }}{% endcapture %}

  {% comment %} Step 2: Pre-scan the collection for matches to avoid empty white space {% endcomment %}
  {% assign has_matching_products = false %}
  {% for p_check in pwp_collection.products %}
    {% if p_check.available and p_check.tags contains discount_tag and p_check.tags contains category_tag %}
       {% assign has_matching_products = true %}
       {% break %}
    {% endif %}
  {% endfor %}

  {% if has_matching_products %}
    <style>
      .pwp-section { margin: 30px 0; position: relative; font-family: inherit; }
      .pwp-title { font-size: 18px; font-weight: bold; margin-bottom: 15px; text-transform: uppercase; color: #000; letter-spacing: 0.5px; }
      .pwp-carousel-wrapper { position: relative; display: flex; align-items: center; }
      .pwp-carousel { display: flex; gap: 15px; overflow-x: auto; scroll-behavior: smooth; scrollbar-width: none; padding: 10px 5px; -webkit-overflow-scrolling: touch; width: 100%; }
      .pwp-carousel::-webkit-scrollbar { display: none; }
      .pwp-card { flex: 0 0 180px; background: #fff; border: 1px solid #e8e8e8; border-radius: 12px; padding: 15px; text-align: center; display: flex; flex-direction: column; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
      .pwp-card img { width: 100%; border-radius: 8px; margin-bottom: 10px; }
      .pwp-card-title { font-size: 13px; height: 34px; overflow: hidden; margin-bottom: 8px; line-height: 1.2; color: #333; }
      .pwp-price-box { margin-bottom: 10px; }
      .pwp-original-price { text-decoration: line-through; color: #999; font-size: 11px; display: block; }
      .pwp-discounted-price { color: #d91212; font-weight: bold; font-size: 16px; display: block; }
      .pwp-save-badge { font-size: 11px; color: #28a745; font-weight: bold; margin-bottom: 10px; display: block; }
      .pwp-add-btn { background: #000; color: #fff; border: none; padding: 10px; border-radius: 6px; width: 100%; cursor: pointer; font-size: 12px; font-weight: bold; transition: background 0.2s ease; }
      .pwp-arrow { background: #fff; border: 1px solid #ddd; border-radius: 50%; width: 32px; height: 32px; display: flex; align-items: center; justify-content: center; position: absolute; z-index: 2; cursor: pointer; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
      .pwp-arrow-left { left: -16px; }
      .pwp-arrow-right { right: -16px; }
    </style>

    <div class="pwp-section">
      <h4 class="pwp-title">PURCHASE WITH PURCHASE {{ discount_num }}% OFF</h4>
      
      <div class="pwp-carousel-wrapper">
        <button type="button" class="pwp-arrow pwp-arrow-left" onclick="this.nextElementSibling.scrollBy({left: -200, behavior: 'smooth'})">&#10094;</button>
        
        <div class="pwp-carousel">
          {% for pwp_product in pwp_collection.products %}
            {% comment %} Step 3: Render only products with BOTH tags {% endcomment %}
            {% if pwp_product.available and pwp_product.tags contains discount_tag and pwp_product.tags contains category_tag %}
              {%- assign price = pwp_product.price -%}
              {%- assign discounted_price = price | times: multiplier -%}
              {%- assign savings_value = price | minus: discounted_price -%}
            
              <div class="pwp-card">
                <img src="{{ pwp_product.featured_image | img_url: '400x400', crop: 'center' }}">
                <p class="pwp-card-title">{{ pwp_product.title }}</p>
                <div class="pwp-price-box">
                  <span class="pwp-original-price">{{ price | money }}</span>
                  <span class="pwp-discounted-price">{{ discounted_price | money }}</span>
                </div>
                <span class="pwp-save-badge">Save {{ savings_value | money }}</span>
                <button type="button" class="pwp-add-btn" data-variant-id="{{ pwp_product.selected_or_first_available_variant.id }}">
                  + ADD BUNDLE
                </button>
              </div>
            {% endif %}
          {% endfor %}
        </div>

        <button type="button" class="pwp-arrow pwp-arrow-right" onclick="this.previousElementSibling.scrollBy({left: 200, behavior: 'smooth'})">&#10095;</button>
      </div>
    </div>

    <script>
    (function() {
      if (window.pwpInitialized) return;
      window.pwpInitialized = true;
      document.addEventListener('click', function(e) {
        if (e.target.classList.contains('pwp-add-btn')) {
          const btn = e.target;
          const pwpId = btn.getAttribute('data-variant-id');
          const form = document.querySelector('form[action*="/cart/add"]');
          if (!form) return;
          const mainId = form.querySelector('[name="id"]').value;
          btn.innerText = "ADDING...";
          btn.disabled = true;
          fetch(window.Shopify.routes.root + 'cart/add.js', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({
              'items': [
                { 'id': parseInt(mainId), 'quantity': 1 },
                { 'id': parseInt(pwpId), 'quantity': 1 }
              ]
            })
          })
          .then(res => res.json())
          .then(data => {
            btn.innerText = "ADDED!";
            btn.style.background = "#28a745";
            document.dispatchEvent(new CustomEvent('cart:updated', { detail: { cart: data } }));
            setTimeout(() => { 
              btn.innerText = "+ ADD BUNDLE"; 
              btn.style.background = "#000";
              btn.disabled = false; 
            }, 2000);
          });
        }
      });
    })();
    </script>
  {% endif %}
{% endif %}
```


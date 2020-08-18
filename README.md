# Woocommerce-Custom-Attribute-Filter
Create custom attribute filter 

//get child categories 
function getColorSearch($atts)
{
  // Here define the product category SLUG
  global $post;
  $post_slug = $post->post_name;
  $category_slug = $post_slug;

  if ($category_slug == "persian-and-modern-rugs") {
    $query_args = array(
      'status'    => 'publish',
      'limit'     => -1,
    );

    $searchUrl = "https://persianandmodernrugs.com/shop/all-products/?post_type=product&filter_colour=";
  } else {
    $query_args = array(
      'status'    => 'publish',
      'limit'     => -1,
      'category'  => array($category_slug),
    );

    $searchUrl = "https://persianandmodernrugs.com/shop/collection/" . $post_slug . "/?filter_colour=";
  }

  $data = array();

  foreach (wc_get_products($query_args) as $product) {

    foreach ($product->get_attributes() as $taxonomy => $attribute) {
      $attribute_name = wc_attribute_label($taxonomy); // Attribute name
      // Or: $attribute_name = get_taxonomy( $taxonomy )->labels->singular_name;

      foreach ($attribute->get_terms() as $term) {

        if ($taxonomy == "pa_colour") {
          $data[$taxonomy][$term->term_id] = $term->name . "|" . $term->slug . "|" . $term->count;
        }
      }
    }
  }

  echo '<ul class="product-categories" style="list-style-type:none;padding:0px;">';

  foreach ($data['pa_colour'] as $key => $value) {

    $atts = explode('|', $value);
    if ($atts[0] != "No images") {

      if ($category_slug == "persian-and-modern-rugs") {

        echo '<li class="cat-item" style="text-transform:uppercase;"><a href="' . $searchUrl . $atts[1] . '">' . $atts[0] . ' <span style="color:#000">(' . $atts[2] . ')</span> </a> </li> ';
      } else {

        $ccount = getTaxo($category_slug, $atts[1], "pa_colour");
        if ($ccount > 0) {
          echo '<li class="cat-item" style="text-transform:uppercase;"><a href="' . $searchUrl . $atts[1] . '">' . $atts[0] . ' <span style="color:#000">(' . $ccount . ')</span> </a> </li> ';
        }
      }
    }
  }

  echo '</ul>';
}
add_shortcode('display_child_color', 'getColorSearch');

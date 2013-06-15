# AMD Loading

## Goal

Add support a flavor of AMD. This allows the code and dependencies to be declared in one place and reduces the reliance on the `lumbar.json` file.

## Requirements

- Side-by-side usage with current declarative lumbar implementation
- Expand AMD to support lumbar module concepts
- Optimize AMD defines into lumbar modules
  - Scope must remain consistent between generated and non-generated code
  - Support source map output
- Must support existing AMD loader plugins

Nice to haves:

- If possible maintain the AMD syntax (Doubtful)
- Allow for runtime use of this feature
- Move all/most lumbar config to `require.config`

## Libraries

- Esprima - Parsing javascript source
- Escodegen - AST serializer

## Usage

WARN: This is currently TBD pending the ability to provide custom plugins for defines in addition to requires.

```
defineRouter('web-checkout', {
      'preload': ['checkout'],
      'routes': {
        'cart': 'cart',
        'cart/:shippingOptionFailed': 'cart',
        'signin': 'signin'
      }
    },
    ['models/cart', 'view!signin/signin', 'view!cart'],
    function(Cart, SigninView, CartView) {

  Phoenix.Router.create(module, {
    cart: function(shippingOptionsFailed) {
      var view = new CartView({ shippingOptionsFailed: cart.saved.length && shippingOptionsFailed });
      if (shippingOptionsFailed) {
        // Remove the shipping options failed url for refresh handling
        Backbone.history.navigate('cart', {trigger: false, replace: true});
      }

      view.bind('complete', function() {
        Phoenix.Track.checkout(cart);
        Phoenix.trackEvent('checkout');
        Backbone.history.navigate('checkout', true);
      });
      view.setModel(Cart.get());
      Phoenix.setView(view);
    },

    signin: function() {
      var view = new SigninView({
        model: Phoenix.authentication
      });
      Phoenix.setView(view);
    }
  });
});

defineView('rich-relevance', ['hbs!additional/template', 'view!carsouel', 'helper!magack'], function() {
});

defineHelper('magack', function() {
  return function(arg, options) {
  };
});
```
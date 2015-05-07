# Kinematics

"Kinematics is the branch of classical mechanics which describes the motion of points, bodies (objects) and systems of bodies (groups of objects) without consideration of the causes of motion." -- [Wikipedia](http://en.wikipedia.org/wiki/Kinematics)

http://recordit.co/LpGzUNsWfI


```html
<div class="flipbook-view bike-360-view -loading">
  <div class="container -full">
    <div class="_image-wrapper">
      <img src="" class="_image">
    </div>
    <div class="slider -horizontal">
      <div class="_track">
        <div class="_control">
          <div class="secondary-button -left -dark">Backward</div>
          <div class="secondary-button -right -dark">Forward</div>
        </div>
        <div class="_axis"></div>
      </div>
      <h4 class="_label">360&deg; View</h4>
    </div>
  </div>
</div>

<script type="text/javascript">
  {% if bike.360_slides.all %}
  window.Yeti.data.bike360Images = [
    {% for slide in bike.360_slides.all %}
    {
      {% thumbnail slide.image 1280x850 as thumb %}
      'url': '{{ thumb.url }}',
      'urlRetina': '{{ thumb.high_resolution.url }}'
    }{% if not forloop.last %},{% endif %}
    {% endfor %}
  ];
  {% endif %}
</script>
```



```css
.flipbook-view {
  display: none;
  position: relative;

  ._image-wrapper {
    padding-bottom: percentage(2/3); // Images have ratio of 2400 / 1600
    position: relative;
  }

  ._cover,
  ._image {
    display: block;
    position: absolute;
    top: 0;
    width: 100%;
  }

  ._cover {
    opacity: 1;
    transition: opacity $trans-duration*6 $trans-function;
    z-index: 2;

    &.-hide {
      opacity: 0;
    }
  }

  .slider {
    opacity: 1;
    transition: opacity $trans-duration $trans-function;
  }

  &.-loading .slider {
    opacity: .4;
  }

  @media (min-width: #{map-get($breakpoints, 'medium')}) {
    display: block;
  }
}
```



```javascript
// Bikes Controller
if (options.data.bike360Images && options.data.bike360Images.length) {
  this.bike360View = new FlipbookView({
    el: options.$el.find('.bike-360-view'),
    images: options.data.bike360Images,
    initialPosition: 0
  });
}

// FlipbookView

var _ = require('lodash');
var DragDealer = require('dragdealer').Dragdealer;

// Preloads images using a promise pattern
require('../lib/jquery.preload');

var FlipbookView = BaseView.extend({

  initialize: function(options) {
    this.images = _.pluck(options.images, options.Modernizr.retina ? 'urlRetina' : 'url');

    this.$slider = this.$('.slider');
    this.$cover = this.$('._cover');
    this.$image = this.$('._image');

    // Set up drag dealer

    var initialPosition = _.isNumber(options.initialPosition) ? options.initialPosition : 0.5;
    this.currentStep = Math.ceil(this.images.length * initialPosition) || 1;

    var dragDealerOptions = {
      disabled: true,
      steps: this.images.length,
      slide: false,
      handleClass: '_control',
      horizontal: this.horizontal,
      vertical: !this.horizontal,
      animationCallback: _.bind(this._handleStep, this)
    };

    // Horizontal slider
    dragDealerOptions.x = initialPosition;

    this.dragDealer = new DragDealer(this.$slider.find('._track')[0], dragDealerOptions);

    // Preload images

    $.preload(this.images).done(_.bind(this._onImagesLoaded, this));
  },

  _onImagesLoaded: function() {
    this.dragDealer.enable();
    this.$el.removeClass('-loading');
  },

  /**
   * Handle step change, updates visible image
   * @param  {integer} x X coorindate for current step position
   * @param  {integer} y Y coordinate for current step position
   * @return {void}
   */
  _handleStep: function(x, y) {
    var targetStep;

    // This is called when drag dealer is first instantiated, so use `this.currentStep`

    if (!this.dragDealer) {
      targetStep = this.currentStep;
    } else {
      targetStep = this.dragDealer.getStepNumber(x);
    }

    if (!!this.dragDealer || targetStep !== this.currentStep) {
      var image = this.images[targetStep - 1];
      this.currentStep = targetStep;

      // If there's a cover and this isn't the initial load, that means it's being interacted
      // with, so fade the cover out

      if (!initial && this.$cover.length && !this.$cover.hasClass('-hide')) {
        this.$cover.addClass('-hide');
      }

      this.$image.attr('src', image);
    }
  }
});

module.exports = FlipbookView;
```

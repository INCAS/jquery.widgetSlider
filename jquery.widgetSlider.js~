/*
 *  Project: INCAS Website
 *  Description: Creating Sliding Content Elements with a Header and a Body
 *  Author: Conrad Barthelmes <INCAS Training und Projekte GmbH & Co. KG>
 *  License: MIT
 *  Version 1.0
 *  Date: 2012-03-16
 *  Template: http://jqueryboilerplate.com/
 */

;(function ( $, window, document, undefined ) {

    // runnin in strict mode
    "use strict";

    // Plugin Defaults
    var pluginName = 'widgetSlider',
        defaults   = {
          header            : 'h2',                         // default header and trigger element -> always visible
          content           : '.content',                   // default content element that is hided
          classOpen         : 'open',                       // default css class added to the wrapper when opened
          classClosed       : 'closed',                     // default css class added to the wrapper when closed
          event             : {
            trigger         : 'click',                      // default event that triggers the hiding/showing
            triggerCustom   : false,                        // whether to trigger the created/open/close custom events
            created         : 'widgetCreated',              // default eventname when created
            open            : 'widgetOpen',                 // default eventname when opened
            close           : 'widgetClose'                 // default eventname when closed
          },
          speed             : 250,                          // open/close speed in ms. set to zero when no effect is needed
          saveState         : true,                         // save the current state in jStorage (requires http://www.jstorage.info/)
          statePrefix       : 'widgetState_',               // simple prefix for local storage keys. header needs it to identify
          openHashId        : true,                         // open anchors when opened and id is equal to header id
          classSkipSave     : 'no-widget-saving',           // classname for widgets that don't need to be saved
          callback          : function(widget) {},          // callback function on every opening/closing action
          callbackOpen      : function(widget) {},          // callback function on opening
          callbackClosed    : function(widget) {},          // callback function on closing
          headerPrevDefault : true                          // whether to prevent the default event or not (when a link is inside and clicked)
        };

    /**
     * main constructor
     *
     * @var DOM Element wrapper 
     * @var Object options 
     */
    function widgetSlider( wrapper, options ) {

        this.options   = $.extend(true, {}, defaults, options);

        this.wrapper   = wrapper;
        this.header    = $(this.wrapper).find(this.options.header).first();
        this.content   = $(this.wrapper).find(this.options.content).first();
        this.id        = $(this.header).attr('id');
        this.wrapperId = $(this.wrapper).attr('id');
        
        
        // dont use those elements that dont have a header or content
        if($(this.header).data('hasWidget') || $(this.header).length == 0 || $(this.content).length == 0) {
          return;
        }

        this._defaults = defaults;
        this._name     = pluginName;
        // boolean to indicate the current state
        this._open     = null;
        // boolean to indicate the current state
        this._closed   = null;
        
        // save reference to plugin in header
        this.header.data(pluginName, this);

        // if jStorage is not avaiable or element has class to not save, dont save
        if($.jStorage == undefined || $(this.header).hasClass(this.options.classSkipSave)) {
          this.options.saveState = false;
        }

        // disable smooth sliding for older IEs
        if($.browser.msie && parseInt($.browser.version, 10) < 8) {
          this.options.speed = 0;
        }

        this.init();
        
    }

    /**
     * initialization logic
     */
    widgetSlider.prototype.init = function () {

        var _plugin = this,
            trigger;

        // use header or trigger
        if(this.options.triggerOnly && this.options.trigger) {
          trigger = $(this.wrapper).find(this.options.trigger);
        }else{
          trigger = $(this.header);
        }

        // bind trigger to event
        trigger.bind(this.options.event.trigger, [], function(ev) {
          if(_plugin.options.headerPrevDefault) {
            ev.preventDefault();
          }
          _plugin.changeState();
        });

        // prevent against multiple slidings
        $(this.header).data('hasWidget', true);
        
        // set initial state
        this.initState();
        
        // fire created event
        if(this.options.event.triggerCustom) {
          this.triggerEvent('created');
        }
    };

    /**
     * reads from css-class or local memory to set the initial state
     */
    widgetSlider.prototype.initState = function() {

      var hash = document.location.hash.substr(1);
      // directly open a widget when called with an anchor in the url
      if(this.options.openHashId && (hash == this.id || hash == this.wrapperId)) {
        this.open(0);
        return;
      }
      
      if(this.readState() === false) {
        this.close(0);
        return;
      } else if(this.readState() === true) {
        this.open(0);
        return;
      } else if($(this.wrapper).hasClass(this.options.classClosed)) {
        this.close(0);
        return;
      } else if($(this.wrapper).hasClass(this.options.classOpen)) {
        this.open(0);
        return;
      }
      
    };

    /**
     * changes the state
     */
    widgetSlider.prototype.changeState = function() {
      
      if($(this.wrapper).hasClass(this.options.classOpen) || $(this.content).filter(':visible').length > 0)
      {
        this.close();
      }
        else
      {
        this.open();
      }
    };

    /**
     * opens the widget
     */
    widgetSlider.prototype.open = function(fx) {

      fx = fx == undefined ? true : fx;
      var _plugin = this;
      this.content.slideDown(fx ? this.options.speed : 0, function() {

        $(_plugin.wrapper).addClass(_plugin.options.classOpen)
        $(_plugin.wrapper).removeClass(_plugin.options.classClosed)

        _plugin.options.callback(_plugin);
        _plugin.options.callbackOpen(_plugin);

        _plugin._open   = true;
        _plugin._closed = false;
        
        _plugin.saveState(true);
        _plugin.triggerEvent(true);
        
      });

    };

    /**
     * closes the widget
     */
    widgetSlider.prototype.close = function (fx) {
      
      fx = fx == undefined ? true : fx;
      var _plugin = this;
      this.content.slideUp(fx ? this.options.speed : 0, function() {
        
        $(_plugin.wrapper).addClass(_plugin.options.classClosed)
        $(_plugin.wrapper).removeClass(_plugin.options.classOpen)

        _plugin.options.callback(_plugin);
        _plugin.options.callbackClosed(_plugin);

        _plugin._open   = false;
        _plugin._closed = true;
        
        _plugin.saveState(false);
        _plugin.triggerEvent(false);
        
      });

    };
    
    /**
     * getter for status if it is open
     */
    widgetSlider.prototype.isOpen = function() {
      return this._open;
    }
    
    /**
     * getter for status if it is closed
     */
    widgetSlider.prototype.isClosed = function() {
      return this._closed;
    }

    /**
     * saves the current state with j.Storage
     */
    widgetSlider.prototype.saveState = function(state) {

      // save state
      if(this.options.saveState && this.id) {
        $.jStorage.set(this.options.statePrefix+this.id, state);
      }
      
    }
    
    /**
     * triggers the events depending on the state
     * 
     * @var mixed state (true|false|'created')
     */
    widgetSlider.prototype.triggerEvent = function(state) {
      
      // fire custom event if needed
      if(this.options.event.triggerCustom) {
        var ev;
        switch(state) {
          case state == true:
            ev =  this.options.event.open;
          break;
          case state == false:
          case state == undefined:
            ev = this.options.event.close;
          break;
          case 'created':
            ev = this.options.event.created;
          break;
        }
        if(typeof(ev) == 'string') {
          $(this.wrapper).trigger(ev, [this]);
        }else if(typeof(ev) == 'function') {
          ev(this);
        }
      }
      
    }

    /**
     * reads the state from jStorage
     */
    widgetSlider.prototype.readState = function() {

      if(this.options.saveState && this.id) {
        return $.jStorage.get(this.options.statePrefix+this.id);
      }

    }

    // A really lightweight plugin wrapper around the constructor,
    // preventing against multiple instantiations
    $.fn[pluginName] = function ( options ) {
        return this.each(function () {
            if (!$.data(this, 'plugin_' + pluginName)) {
                $.data(this, 'plugin_' + pluginName, new widgetSlider( this, options ));
            }
        });
    }

})(jQuery, window, document);
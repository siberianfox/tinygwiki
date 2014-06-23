This page is a place holder to describe sending files over the network to a SPJS listener (BBB or Raspberrypi)

# Hello World Widget

If you are going to create a new widget in ChiliPeppr, here's a Hello World widget to give you a very simple view of what the most basic widget looks like.

HTML

    <div id="com-chilipeppr-widget-hello" class="panel panel-default">
        <div class="panel-heading">
            <div class="btn-toolbar pull-right" role="toolbar" >
                <div class="btn-group">
                </div>
                <div class="btn-group">
                    <div class="dropdown">
                    <button type="button" class="btn btn-xs btn-default dropdown-toggle" data-toggle="dropdown"><span class="caret"></span></button>
                    <ul class="dropdown-menu dropdown-menu-right" role="menu">
                    </ul>
                    </div>
                </div>
            </div>
            <span class="panel-title" data-toggle="popover">Hello World</span>
        </div>
        <div id="com-chilipeppr-widget-hello-body" class="panel-body">
            Hello World
        </div>
        <div id="com-chilipeppr-widget-hello-ftr" class="panel-footer hidden">
    </div>


Javascript

    // Test this element. This code is auto-removed by the chilipeppr.load()
    cprequire_test(["inline:com-chilipeppr-widget-hello"], function (hello) {
        console.log("test running of " + hello.id);
        hello.init();
    
    } /*end_test*/ );
    
    cpdefine("inline:com-chilipeppr-widget-hello", ["chilipeppr_ready"], function () {
        return {
            id: "com-chilipeppr-widget-hello",
            url: "http://fiddle.jshell.net/chilipeppr/udLG7/show/light/",
            fiddleurl: "http://jsfiddle.net/chilipeppr/udLG7/",
            name: "Widget / Hello",
            desc: "This widget is just a hello world example.",
            publish: {
            },
            subscribe: {},
            foreignPublish: {
            },
            foreignSubscribe: {
            },
            init: function () {
    
                this.forkSetup();
                
                console.log(this.name + " done loading.");
            },
            forkSetup: function () {
                var topCssSelector = '#' + this.id;
                
                $(topCssSelector + ' .panel-title').popover({
                    title: this.name,
                    content: this.desc,
                    html: true,
                    delay: 200,
                    animation: true,
                    trigger: 'hover',
                    placement: 'auto'
                });
                
                var that = this;
                chilipeppr.load("http://fiddle.jshell.net/chilipeppr/zMbL9/show/light/", function () {
                    require(['inline:com-chilipeppr-elem-pubsubviewer'], function (pubsubviewer) {
                        pubsubviewer.attachTo($(topCssSelector + ' .panel-heading .dropdown-menu'), that);
                    });
                });
                
            },
        }
    });
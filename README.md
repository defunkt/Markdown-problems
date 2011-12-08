# simple_field

simple_field allows you to write one view and then use that view for both show and create/update actions.
To accomplish this, you use the simple_field method in your view (it depends on simple_form) and then
provide two shared views: 

```ruby  
  shared/_simple_field.haml.html 
  shared/_simple_field_show.haml.html
```
which process the output appropriately.  

## Installation

In Rails3, add this to your Gemfile

```bash
gem 'simple_field', :git => 'git@github.com:JESii/simple_field.git'
```
and then run 

```bash
bundle install
```

## Getting Started

simple_field allows the use of a single view (such as app/views/_form.html.haml) for both show and modify actions by determining
if you are are in "display" (or "show") mode (e.g., read) or "modify" mode (e.g., create/update/delete) and then using one of two partials
that you define: one for show mode and the other for modify mode.

The code that is generated uses simple_form calls and positions the label and the actual form field using the
blueprintCSS features, primarily the span class definitions. It assumes that you have a "label" and a "field", and allows
you to position both elements precisely within the blueprint grid.  The basic layout is as follows:

    | prefix | Label | middle | Field |  

where:  
  * prefix  - Empty space (i.e., number of empty grid elements) before the Label
  * Label   - The label text, positioned in the desired number of grid elements
  * middle  - Empty space (empty grid elements) between the Label and the Field
  * Field   - The actual Field, either text (in show mode) or an input field (in modify mode), with defined grid positioning

Using just simple_form and blueprint, you'd have to code something like this (using Haml instead of ERB):

```haml
%span-2
  &nbsp
%span-2
  f.label form_field
%span-1
  &nbsp
%span{:class => 'span-10 last'}
  f.input form_field, :label => false, :size => 25
```

(NOTE: the '&nbsp' codes are there so that blueprint properly formats the grid; without them, the empty cells take up no space.)

With simple_field, you code it like this:

```haml
=simple_field(f, "Phone#", "phone_no", :ps => 2, :ls => 2, :ms => 1, :fs => 10, :size => "25", :last => true )
```
where:  
  * ps    - prefix span size
  * ls    - Label span size
  * ms    - middle span size
  * fs    - Field span size
  * size  - Input field size
  * last  - Blueprint's "last" field specification

## Shorthand for blueprint span specification

Instead of coding all the options as hash entries (the :ps, :ls, ... entries), a shorthand notation is available:

  [Pn]Ln[Mx]Fn[L]

where the [...] elements are optional and:
  * Pn    - prefix span size
  * Ln    - Label span size
  * Mn    - middle span size
  * Fn    - Field span size
  * L     - Blueprint's "last" field specification

Therefore, with the shorthand notation, you could instead code the following for the previous example:

```haml
=simple_field f, "Phone#", "phone_no", P2L2M1F10L, :size => "25"
```

## Partials for displaying the results

The simple_field gem uses two partials to actually display each field, one for show actions and one for modify actions.  The two views are described below:

### shared/_simple_field.haml.html

```haml
- form_field = to_sym
%span{:class => "span-#{lspan}"}
  = f.label form_field
%span{:class => "span-#{fspan} #{last}"}
  - if as.blank? then
    = f.input form_field, :label => false, :input_html => { :class => "form_field", :size => size }
  -else
    = f.input form_field, :as => "#{as.to_sym}", :label => false, :input_html => { :class => "form_field", :size => size }
```  

### shared/_simple_field_show.haml.html

```haml
- form_field = to_sym
%span{:class => "span-#{lspan}"}
  = f.label form_field 
%span{:class => "span-#{fspan} #{last} form_field"}
  -result = f.object.send(form_field)
  -result = '&nbsp;' if result.nil? || result.to_s == ''
  -result = result.to_s.html_safe if result.respond_to? :to_s
  - if as.blank? then
    =result
  -else
    =result
```

## Usage examples

Once you have installed the gem, defined your two views (haml, erb, or whatever templating system you are using), added the 
simple_field partials, you're ready to start using it for view goodness. Here are some examples.

```haml
.span-24
  =simple_field(f, "Driver", "drivers_name", :ls => 2, :fs => 10, :size => "25" )
  =simple_field(f, "Company", "company_name", :ls => 2, :fs => 10, :size => "25", :last => true )
.span-24
  =simple_field(f, "Policy#", "policy_no", :ls => 2, :fs => 10, :size => "25" )
  =simple_field(f, "Phone#", "phone_no", :ls => 2, :fs => 10, :size => "25", :last => true )
.span-24
  =simple_field(f, "Drv/L#", "drivers_license_no", :ls => 2, :fs => 10, :size => "15" )
  =simple_field(f, "Truck No", "truck_no",  :ls => 2, :fs => 10, :size => "10", :last => true )

.span-24
  .span-2.form_label
    Vehicle
  .span-22.last
    %p{ :style => "margin-top: 10px; margin-bottom: 0px;  border-bottom: 2px solid #000;" }
.span-24
  =simple_field(f, "Make", "vehicle_make",  :ls => 2, :fs => 4, :size => "10" )
  =simple_field(f, "Model", "vehicle_model",  :ls => 2, :fs => 4, :size => "10" )
  =simple_field(f, "Yr", "vehicle_year",  :ls => 2, :fs => 4, :size => "10" )
  =simple_field(f, "Vin", "vehicle_vin_no",  :ls => 2, :fs => 4, :size => "15", :last => true )
```
## Contributing to simple_field
 
* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it in a future version unintentionally.
* Please try not to mess with the Rakefile, version, or history. If you want to have your own version, or is otherwise necessary, that is fine, but please isolate to its own commit so I can cherry-pick around it.

## Copyright

Copyright (c) 2011 Jon Seidel. See LICENSE.txt for
further details.

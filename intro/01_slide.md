<!SLIDE center>
# A Gestalt Informant #
 <span id="title">
 ![letter](../img/letter.png)
</span>

## Heavy Water Software ##
### #ChefConf 2012 ###

<!SLIDE bullets incremental transition=scrollUp>
# ge·stalt : #
##  [guh-shtahlt, -shtawlt, -stahlt, -stawlt] ##

* a configuration ... having specific properties that cannot be derived from the summation of its parts; a unified whole.

* an instance or example of such a unified whole.

.notes first, maybe a definition is in order. who's heard of the term "gestalt"?

<!SLIDE center incremental transition=growX>

* In art:
![art gestalt](../img/design-gestalt.png)

* In psychology:
* Brain possesses self-organizing tendencies
* Perception is the product of complex interactions among various
stimuli

.notes used in both art and psychology, wish I could say that was my inspiration

<!SLIDE center transition=curtainX>

![devastator](../img/devastator_layout.png)

.notes I got it from Transformers. all these little guys would combine

<!SLIDE smaller center incremental transition=fadeZoom>

![devastator](../img/devastator-outofwater.png)

* Transformers &#169;Hasbro, Thanks. {◕ ◡ ◕}

<!SLIDE center incremental>
# Monitoring is Important #
##[duh]##

* You can't manage what you haven't measured

.notes I actually heard this phrase from an acquaintance who does
building assessments to project long term maintenance costs. If it
applies in the case of physical buildings, how much more so in our
field where assets can often seem much less tangible.

<!SLIDE center incremental>
# Monitoring is hard #

* Just because it's up doesn't mean it's working

<!SLIDE center incremental>
# Distributed systems are complex #

* Failures often a result of a confluence of events, no single root cause

.notes and other reasons

<!SLIDE subsection>
# gdash #

<!SLIDE code >
# ::Resources #
## gdash_dashboard ##

    @@@ Ruby
    def initialize(*args)
      super
      @action = :create
    end

    actions :create, :delete

    attribute :category, :kind_of => String, :required => true
    attribute :description, :kind_of => String, :required => true
    attribute :display_name, :kind_of => String, :required => false

<!SLIDE code >
#  ::Resources #
## gdash_dashboard_component ##

    @@@ Ruby
    def initialize(*args)
      super
      @action = :create
    end

    actions :create, :delete

    attribute :lines, :kind_of => Array, :required => false

    %w(forecasts fields).each do |attr_hash|
      attribute attr_hash, :kind_of => Hash, :required => false
    end

    %w(dashboard_name dashboard_category).each do |attr_string_req|
      attribute attr_string_req, :kind_of => String, :required => true
    end

    hash_attrs = %w(warning critical)
    hash_attrs.each do |attr_hash|
      attribute attr_hash, :kind_of => String, :required => false
    end
    
    ... "There's more clever stuff, go check out the code!"

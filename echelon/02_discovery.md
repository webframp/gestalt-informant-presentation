<!SLIDE subsection>
# Discovery
## [I'm in your infrastructure, finding your stuff]

<!SLIDE code small>
# A simple search wrapper

    @@@ Ruby
    host = Discovery.search("any_role",
                            :node => node,
                            :environment_aware => false)
    


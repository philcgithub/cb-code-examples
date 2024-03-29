# Using DSL #

## Viewing results of api calls #

Return values from DSL procedures behave different from groovy.  In groovy you would be able to do something like:

    def result = ef.getGroups()
    println result

and you would see the response string.

But with DSL (using the DSLIDE), you don't see any value.  Do the following instead:

    def result = getGroups()
    result

The result is an ArrayList containing groups.  If you do the following:

    def result = getGroups()
    println "$result"

Then you will see the reference to the arraylist instead.

## Accessing elements ##

To access elements in that arraylist you can do:

    def result = getGroups().groups[0]
    result

This will show the first element, and then you can access properties inside that element, e.g.:

    def result = getGroups().groups[0].owner
    result

And now we are dealing with strings you can do:

    def result = getGroups().groups[0].owner
    println result

To get a list of all the group names:

    def result = getGroups().groups.groupName // note: name also works here instead of groupName
    result

Or another way is to write it like this to capture all the results:

    groups = []
    getGroups().each { groups.push(it) }
    groups

Or to just capture the name of each result (note the it.name refers to the collection and not any of the values in the result):

    groups = []
    getGroups().each { groups.push(it.name) } // OR getGroups().each { groups.push(it.groupName) }
    groups

The following also works:

    def result = getGroups().groups
    println result
    result.each { Result ->
    	println Result.groupName
    }

## Finding matches ##

If you want to look for a specific match in the result (e.g. for a parameter validation) then the following works:

    groups = []
    getGroups().each { groups.push(it.name) }
    groups
        if (groups.any { it == "greadonly" }) {
        println "Yes"
    } else {
        println "No"
    }

As does this:

    if ( getGroups().groups.name.any { it == "greadonly"  } ) {
        println "Yes"
    } else {
        println "No"
    }

OR to make the server filter instead:

    if ( getGroups(filter: "greadonly").groups.groupName ) {
        println "Yes"
    } else {
        println "No"
    }

And if you want to find the value of a property from a specific element (and you know the name of the element):

    groups = []
    owner = getGroups().groups.find{it = "Testing"}.owner
    owner

Or to get the owner property when you know the groupName value:

    groups = []
    owner = getGroups().groups.find{it.groupName == "Testing"}.owner
    owner

OR

    def result = getGroups().groups
    result.each { Result ->
    if (Result.groupName == "Testing" ) {
        println Result.owner
    }
    }

## Specifing parameter names ##

When using the DSL IDE it seems to be fairly forgiving on whether you explicitly name the input parameters or not, but other use of DSL (e.g. Service Catalogs Import from DSL) seem to be less forgiving.  It's safer to include the parameter name always.  E.g.:

Instead of

    def Host = getProperty("/server/hostName").value

It is better to use

    def Host = getProperty(propertyName: "/server/hostName").value

## Identifying properties ##

To find the properties available for different objects, use getProperties.  For example:

    def props = getProperties(propertyName: "/server/settings")
    props

Will show all the available server settings and their property names.   You can also start with just /server to start at the very top level and work your way down.

To simplify the results and just see the list of property names:

    def props = getProperties(propertyName: "/server/settings").property.propertyName
    props
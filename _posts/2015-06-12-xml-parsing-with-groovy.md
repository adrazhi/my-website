---
layout: post
title: XML Parsing with Groovy
---

[Groovy](http://www.groovy-lang.org) is a powerful, optionally typed and dynamic language and in this post i will show how easily you can parse XML by using a few lines of code. So, let's do it.

The XML used for this example contains a list of consumers:

{% highlight groovy %}
def xml = """
<consumers>
    <consumer>
        <name>Albano Drazhi</name>
        <email>example@domain.com</email>
    </consumer>
    <consumer>
        <name>John Doe</name>
        <email>john@example.com</email>
    </consumer>
</consumers>
"""
{% endhighlight %}

Let's assume that you want to check if a consumer exists by matching an email:

{% highlight groovy %}
def email_to_match = "fabio@example.com"
{% endhighlight %}

So, just parse XML and return `true` if the consumer's email exists in the XML:

{% highlight groovy %}
def consumers = new XmlParser().parseText(xml)

for (consumer in consumers) {
    if (email_to_match.equals(consumer.email.text())) {
        return true
    }
}

return false
{% endhighlight %}

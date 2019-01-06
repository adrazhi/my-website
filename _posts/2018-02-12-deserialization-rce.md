---
layout: post
title: Jackson Deserialization Remote Code Execution
---

## Background

Jackson is an open source Java serialization and deserialization tool that serializes Java objects into xml or json format strings, or deserializes them back to corresponding objects. Because of its ease of use, it is faster and does not depend on it. 

## Vulnerability Trigger Chain

`getOutputProperties() → newTransFormer() → newInstance()`


## Construction method
The serialized malicious attack code is constructed by the serialization method of the third-party inventory in the Java ecosystem , and the malicious code base64 is encoded and stitched into a well-constructed json data packet. If the code layer is using the `ObjectMapper` object instance 
`(ObjectMapper mapper) = new ObjectMapper () ;)` Open the enableDefaultType property (such as `mapper.enableDefaultTyping ();)`, will automatically call the third-party library deserialization method for the incoming JSON in readValue, resulting in code execution.

## Official repair method
After the vulnerabilities were generated, the official blacklisted banned code execution vulnerabilities caused by the deserialization problem of third-party libraries in the blacklist.

The blacklist is as follows: 

	org.apache.commons.collections.functors.InvokerTransformer 
	org.apache.commons.collections.functors.InstantiateTransformer 
	org.apache.commons.collections4.functors.InvokerTransformer 
	org.apache.commons.collections4.functors.InstantiateTransformer 
	org.codehaus.groovy. runtime.ConvertedClosure 
	org.codehaus.groovy.runtime.MethodClosure 
	org.springframework.beans.factory.ObjectFactory 
	com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl


## The emergence of new vulnerabilities (CVE-2017-17485)
It is well known that blacklists are an unreliable fix. Attackers can often bypass the blacklist by some means, causing the vulnerability.

Security researchers have found that Spring can be abused by Jackson-databind to execute arbitrary commands with `enableDefaultTyping()` turned on.



## Repair Suggestion

- Disable the `enableDefaultTyping()` method;
- Filter or type check the JSON data passed in by the client.

# Learn By Example

DictShield has been useful to me for several different use cases.  This document
attempts to explain the way each demo operates and how it solves modeling
problems cleanly.

The first demo covers the basics of designing models.  The second describes
validating data against model designs with instances of models or classmethods.
The last demos cover modeling fields that contain lists or lists of embedded
objects or lists of embedded objects that each contain lists of embedded
objects.


## Basic Objects

This class demonstrates the basics of building `Document` classes to model a
simple class hierarchy.  We see a somewhat open-ended `Media` class with a
`title` field and after that there is a `Movie` class that subclasses `Media`.

The class hierarchy is stored as the `_types` field on instances, but appears
in the serialized forms too.  The class name itself is stored as `_cls`.
Anything else on the structure is a field you import from `dictshield.fields`.  
In the demo we see a StringField, ObjectIdField and an IntField used.

After instantiating some classes we see some of the `make*safe` functions.
These functions serialize the data but allow use of a white list or black list
for fields.  This makes the typical process of sending data to the public or a
user's mobile device fairly simple.

The basic steps shown in this demo are:

1. A `Media` instance in JSON form
2. A `Movie` instance in JSON form
3. The output from calling `make_json_ownersafe` on the `Movie` instance
4. The output from calling `make_json_publicsafe` on the same `Movie` instance

* https://github.com/j2labs/dictshield/blob/master/demos/basic_objects.py


## Validation

This demo shows some of DictShield's validation abilities. We see a `User` model
that has a function for setting the secret field, which is just an MD5. A
`set_password`

Two primary ways of validating data is shown.  The first is to instantiate a 
model instance with data.  The second is to pass a dictionary through the
classmethod's available on the model.  

This demo also introduces `_private_fields` and `_public_fields`, which act as
a black list and a white list for serialization.  Any fields in
`_private_fields` are considered not suitable for transmitting outside your
system. And fields listed in `_public_fields` are considered the only fields
suitable for sharing with the general public.

Both functions have been valuable tools for me while building API's.

* https://github.com/j2labs/dictshield/blob/master/demos/validation.py


## Lists and Embedded Documents

DictShield allows lists and dictionaries to be represented as fields.  These
lists might consist of `StringField` instances or it might consist of
`EmbeddedDocument` instances.


### Simple Lists

In `simple_lists.py` we see an `Action` model, which is a simple way to
associate some value with a list of tags.  Not particularly useful, but supports
the demo anyway.

A `SingleTask` instance associates a single `Action` instance with a creation
date.

A `TaskList` associates a list of `Action` instances with a creation date but
also adds an updated date and a count of how many tasks are completed.  This
model is hypothetical, but it assumes the tasks are done synchronously so then
`num_completed` would be an offset from the start of the task list.

First, the `Action` instance is serialized to Python and then JSON.  Then the
`SingleTask` instance is created and serialized to Python and JSON.  And a
`TaskList` is handled the same.

* https://github.com/j2labs/dictshield/blob/master/demos/simple_lists.py


### Embedded Documents

There is a `Customer` instance with the name "Ben G".  This customer has ordered
two types of bowls: A Japanese Bowl and an African Bowl.  The `Customer`
instance contains a list of `Order` instances, which themselves contain a list
of `Product` instances..

To keep things simple the two bowls, represented with two `Product` instances,
are sold to our customer in a single `Order` instance.

The whole structure is then serialized to both Python and JSON.

The resulting JSON data is then used to create a new instance of the `Customer`
with a single `Order` containing both `Product` instances.  Going to and from
JSON is easy, even with complicated structures of data.

* https://github.com/j2labs/dictshield/blob/master/demos/embedded_documents.py

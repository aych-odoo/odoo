# Real Estate: Server Framework 101

This addon follows the [Odoo 20.0 Server framework 101 tutorial](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101.html) one chapter at a time. Each commit contains the completed work for one chapter. Read the chapter, inspect its commit with `git show`, and try the behavior in Odoo before moving to the next commit.

## Chapter 1 - Architecture overview

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/01_architecture.html)

Odoo separates what you see in the browser, the Python code that handles business rules, and the PostgreSQL database that stores records. An addon brings related pieces together. Its models describe business data, views describe how that data appears, and XML or CSV files can create menus, permissions, and other records.

This first commit contains only this README. Before creating the addon, it helps to know where each part of the real estate application belongs and why installing an addon affects a particular database.

**Try it:** Find an existing addon in the repository. Locate its manifest, a model file, and a view file.

## Chapter 2 - A new application

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/02_newapp.html)

An Odoo addon needs a Python package and a manifest before Odoo can discover it. This chapter adds `__init__.py` and `__manifest__.py` to `estate`. The manifest gives the addon a name, declares its dependency on `base`, and sets `application=True` so it appears under the Apps filter.

The addon is an empty shell at this point. It can be installed, but it has no model or menu yet. That separation makes it easier to see what the manifest does before business features are added.

## Chapter 3 - Models and basic fields

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/03_basicmodel.html)

The new `estate.property` model gives the application somewhere to store property records. Its Python class defines fields for the name, description, postcode, availability, prices, rooms, area, and garden details. The ORM maps the model to a PostgreSQL table named `estate_property`.

`name` and `expected_price` are required because a useful property record needs both. `garden_orientation` stores one of four keys while showing readable labels in the interface. Odoo also adds fields such as `id` and `create_date` automatically; they do not need declarations in our class.

**Try it:** Upgrade `estate` and inspect the `estate_property` table. Compare its columns with the Python fields, then find an automatic field that was not declared in `estate_property.py`.

## Chapter 4 - Security: a brief introduction

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/04_securityintro.html)

A model does not become available to every user merely because it exists. This chapter adds an access row for `base.group_user`, granting internal users create, read, update, and delete access to properties. The manifest loads that row when the addon is installed or upgraded.

The tutorial text shows the older `ir.model.access.csv` layout. This Odoo 20 checkout uses `security/ir.access.csv`, where the `operation` column lists the granted operations. The underlying idea is the same: permissions are data loaded by the module, and a menu alone does not grant model access.

**Try it:** Upgrade `estate` and check that its missing-access warning is gone. Compare property access for an internal user with access for a user outside that group.

## Chapter 5.1 - Actions and menus

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/05_firstui.html)

A window action tells Odoo which model to open. The new menu path, Real Estate > Advertisements > Properties, links to that action. The manifest loads the action before the menus so the menu can refer to it.

Odoo can display the existing `estate.property` fields using its generated list and form views. A custom view layout comes in Chapter 6; the menu already makes the model usable.

**Try it:** Open Real Estate > Advertisements > Properties, create a property, and inspect the generated list and form views.

## Chapter 5.2 - Field attributes and defaults

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/05_firstui.html)

The property model now supplies values that make new records ready to use. A new property starts with two bedrooms, an availability date three months ahead, and state New. The `active` field enables Odoo's standard archive behavior, while `state` records where the property is in its sales lifecycle.

Availability date, selling price, and state use `copy=False`, so duplicating a property does not carry over those values from the original. Selling price is read-only in the form because accepting an offer will set it later. The defaults and copy behavior live on the model, so they also apply when records are created or duplicated through code.

**Try it:** Create and duplicate a property. Compare their availability dates, selling prices, and states, then archive one property and look for it in the normal list.

## Chapter 6.1 - List view

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/06_basicviews.html)

Odoo can generate a list view, but a declared view lets us choose the columns that matter when scanning properties. The new list shows each property's name, postcode, bedrooms, living area, expected and selling prices, and availability date. The existing property action opens this view when users select Properties from the menu.

The XML view controls how the model's fields appear; it does not add or change database fields. Later views can present the same records in different ways.

**Try it:** Open Properties and compare the displayed columns with the fields in `estate.property`. Change a column's position in the XML view and observe how the list changes.

## Chapter 6.2 - Form view

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/06_basicviews.html)

A property needs more space for editing than a row in the list provides. The form view places the property name at the top, groups location and pricing details side by side, and puts descriptive features on a Description tab. Groups and pages organize existing model fields without changing how they are stored.

The Properties action already offers a form view. With this declaration installed, opening a property uses the designed layout instead of Odoo's generated form.

**Try it:** Open a property from the list and find its postcode, expected price, and garden details. Move a field between groups in the XML and see where it appears in the form.

## Chapter 6.3 - Search view

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/06_basicviews.html)

The search view lets users find properties by name, postcode, living area, or bedroom count. Its Available filter selects properties in New or Offer Received state. The Postcode group option arranges matching properties by location.

The filter's domain decides which records are included; the grouping context changes how those records are displayed. Both operate on the existing property model, with no new fields.

**Try it:** Create properties in two postcodes. Search by name, apply Available, and group by postcode. Change a property's state and see whether it still appears in the filtered results.

## Chapter 7.1 - Many2one fields

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/07_relations.html)

`Many2one` links a property to one record in another model. Properties now link to a property type, a buyer, and a salesperson. The property type is a new model with its own access right, views, and Configuration menu. Buyers use the existing `res.partner` model, while salespeople use `res.users`, so the addon reuses Odoo's contacts and users.

The type appears in the property list, form, and search views. The buyer and salesperson appear on an Other Info tab. The salesperson defaults to the current user, while a duplicated property does not copy its buyer.

**Try it:** Create a property type, assign it to several properties, and search by type. Open Other Info and compare the buyer and salesperson fields with the records they reference.

## Chapter 7.2 - Many2many fields

[Official chapter](https://www.odoo.com/documentation/20.0/developer/tutorials/server_framework_101/07_relations.html)

Properties can now have several tags, and a tag can describe several properties. The `tag_ids` `Many2many` field stores that shared classification without duplicating tag names on every property. The new tag model has its own access right, views, and Configuration menu.

The `many2many_tags` widget shows tags compactly in the property list and form. This changes their presentation while the relationship remains a set of linked tag records.

**Try it:** Create a tag such as Renovated, assign it to two properties, and add multiple tags to one property. Remove a tag from one property and check that the tag record still exists.

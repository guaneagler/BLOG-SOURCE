---
title: Drupal page
date: 2018-06-06 10:42:47
categories: Drupal
tags:
	- drupal
	- panelizer
	- page
description: 
---

### Drupal 8 panelizer

#### Configuration
- Enable module panelizer (depending on panel, ctools)
- Manage display, check 'Panelize this view mode', save
- After save, we can config panelizer for this display by click 'Edit'
- In the panelizer options section, we can check option to make each node panelizer separtely.
#### Features
- Node elements can be put in different sections.
- Add other block in page sections.
- Custom echo node separtely.
- Add validate rule for the panelizer page. 

### Display Suite (ds)

#### Configuration
- Enable ds module
- Manage display, select one layout for the display, then save it.
- We can see many disabled fields appear, these are fields other module provide by code. We can add our fields. The fields we talk about are not the same as field like content type field.
- Open 'admin/structure/ds/fields', 'Add a block field'. We can choose which entity the block will be available and choose the block we want to add. Add a label name, then save it.
- After save, go back to the 'manage display' setting. We can find the field

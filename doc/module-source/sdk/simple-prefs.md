<!-- This Source Code Form is subject to the terms of the Mozilla Public
   - License, v. 2.0. If a copy of the MPL was not distributed with this
   - file, You can obtain one at http://mozilla.org/MPL/2.0/. -->

<!-- contributed by Erik Vold [erikvvold@gmail.com]  -->

<div class="experimental">
<br>

**This module is currently experimental.**

**Based on the feedback we get from users, we expect to change it,
and may need to make incompatible changes in future releases.**

</div>

The `simple-prefs` module lets you store preferences across
application restarts. You can store booleans, integers, and string
values, and users can configure these preferences in the
[Add-ons Manager](https://support.mozilla.org/en-US/kb/Using%20extensions%20with%20Firefox#w_how-to-change-extension-settings).

This gives users a consistent way to access and modify
preferences across different add-ons.

To define preferences and give them initial values, add a new JSON array
called `preferences` to your [package.json](dev-guide/package-spec.html) file,
and give it one entry for each preference:

    {
        "fullName": "Example Add-on",
        ...
        "preferences": [{
            "name": "somePreference",
            "title": "Some preference title",
            "description": "Some short description for the preference",
            "type": "string",
            "value": "this is the default string value"
        },
		{
            "description": "How many of them we have.",
            "name": "myInteger",
            "type": "integer",
            "value": 8,
            "title": "How Many?"
        }]
    }

## Setting Attributes ##

### Mandatory Common Attributes ###

These are attributes that all settings *must* have.

<table>
  <colgroup>
    <col width="15%">
    <col width="85%">
  </colgroup>
  <tr>
    <th>Attribute</th>
    <th>Description</th>
  </tr>

  <tr>
    <td><code>type</code></td>
    <td>The type of setting, as defined in the
	<a href="modules/sdk/simple-prefs.html#Setting_Types">"Setting Types"</a>
	section below.</td>
  </tr>

  <tr>
    <td><code>name</code></td>
    <td><p>An identifier for the setting.
	This is used to access the setting from your add-on:</p>
	<pre><code>console.log(require("simple-prefs").prefs.mySettingName);</code></pre>
	<p>This means that it must be a valid JavaScript identifier.</p></td>
  </tr>

  <tr>
    <td><code>title</code></td>
    <td>This is used as a label for the setting in the
	Add-ons Manager user interface.</td>
  </tr>

</table>

### Optional Common Attributes ###

These are attributes that all settings *may* have:

<table>
  <colgroup>
    <col width="15%">
    <col width="85%">
  </colgroup>

  <tr>
    <th>Attribute</th>
    <th>Description</th>
  </tr>

  <tr>
    <td><code>description</code></td>
    <td>This appears below the setting title in the Add-ons Manager UI.</td>
  </tr>

  <tr>
    <td><code>value</code></td>
    <td>A default value for the setting. Depending on the setting type,
	this may be an integer, string, or boolean value.</td>
  </tr>

</table>

### Setting-Specific Attributes ###

These are settings that are only applicable to certain setting types.
They are documented along with the
[setting types themselves](modules/sdk/simple-prefs.html#Setting_Types).

## Setting Types ##

The setting types map to the
[inline settings types](https://developer.mozilla.org/en/Extensions/Inline_Options)
used by the Add-on Manager. All the inline settings are supported except
for `radio` and `menulist`.

<table>
    <tr>
        <th>Type</th>
        <th>Description</th>
        <th>Example Specification</th>
    </tr>
	<tr>
		<td><code>bool</code></td>
		<td>Displayed as a checkbox and stores a boolean.</td>
		<td><pre>
{
    "description": "Does it have tentacles?",
    "type": "bool",
    "name": "hasTentacles",
    "value": true,
    "title": "Tentacles"
}</pre></td>
	</tr>
	<tr>
		<td><code>boolint</code></td>
		<td><p>Displayed as a checkbox and stores an integer.</p>
			<p>A boolint is presented to the user as a checkbox,
			but instead of storing <code>true</code> or <code>false</code>,
			the "on" or "off" checkbox states are mapped to
			integers using "on" or "off" properties in the
			specification.</p>

			<p>To provide this mapping the boolint requires
			two mandatory attributes called "on"
			and "off", both of which are supplied as strings.</p>

			<p>Note that even so, the "value" property is
			supplied as an integer.</p></td>
		<td><pre>
{
    "type": "boolint",
    "name": "myBoolint",
    "on": "1",
	"off": "2",
	"value": 1,
	"title": "My Boolint"
}</pre></td>
	</tr>
	<tr>
		<td><code>integer</code></td>
		<td>Displayed as a textbox and stores an integer.</td>
		<td><pre>
{
    "description": "How many eyes?",
    "type": "integer",
    "name": "eyeCount",
    "value": 8,
    "title": "Eye count"
}</pre></td>
	</tr>
	<tr>
		<td><code>string</code></td>
		<td>Displayed as a textbox and stores a string.</td>
		<td><pre>
{
    "type": "string",
    "name": "monsterName",
    "value": "Kraken",
    "title": "Monster name"
}</pre></td>
	</tr>
	<tr>
		<td><code>color</code></td>
		<td>Displayed as a colorpicker and stores a string
			in the <code>#123456</code> format.</td>
		<td><pre>
{
    "type": "color",
    "name": "highlightColor",
    "value": "#6a5acd",
    "title": "Highlight color"
}</pre></td>
	</tr>
	<tr>
		<td><code>file</code></td>
		<td>Displayed as a "Browse" button that opens a file picker
			and stores the full path and name of the file selected.</td>
		<td><pre>
{
    "type": "file",
    "name": "myFile",
    "title": "Select a file"
}</pre></td>
	</tr>
	<tr>
		<td><code>directory</code></td>
		<td>Displayed as a "Browse" button that opens a directory picker
			and stores the full path and name
			of the directory selected.</td>
		<td><pre>
{
    "type": "directory",
	"name": "myDirectory",
	"title": "Select a directory"
}</pre></td>
	</tr>
	<tr>
		<td><code>control</code></td>
		<td><p>Displays a button.</p>
			<p>When the user clicks the button,
			the function listening to the <code>on()</code>
			function for this preference is called.</p>

			<p>This type requires an mandatory attribute
			called "label" which is provided as a string.
			It is used to label the button.</p></td>
		<td><p>In "package.json":</p><pre>
{
    "type": "control",
    "label": "Click me!",
    "name": "sayHello",
    "title": "Say Hello"
}</pre>
           <p>In "main.js":</p><pre><code>
var sp = require("simple-prefs");
sp.on("sayHello", function() {
  console.log("hello");
});</code></pre></td>
	</tr>
</table>

## Localization ##

Using the SDK's localization system, you can provide translated forms
of the `title` and `description` attributes. See the
[localization tutorial](dev-guide/tutorials/l10n.html#Using Localized Strings in Preferences)
for more details.

<api name="prefs">
@property {object}
  *experimental* A persistent object private to your add-on.  Properties with boolean,
  number, and string values will be persisted in the Mozilla preferences system.
</api>


<api name="on">
@function
  *experimental* Registers an event `listener` that will be called when a preference is changed.

**Example:**

    function onPrefChange(prefName) {
        console.log("The " + prefName + " preference changed.");
    }
    require("simple-prefs").on("somePreference", onPrefChange);
    require("simple-prefs").on("someOtherPreference", onPrefChange);


@param prefName {String}
  The name of the preference to watch for changes.
@param listener {Function}
  The listener function that processes the event.
</api>

<api name="removeListener">
@function
  *experimental* Unregisters an event `listener` for the specified preference.

@param prefName {String}
  The name of the preference to watch for changes.
@param listener {Function}
  The listener function that processes the event.
</api>


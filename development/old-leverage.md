<!-- TITLE: Old Leverage -->
<!-- SUBTITLE: A quick summary of what Old Leverage is -->

# Introduction

> **Who should be doing this:** Designers or developers working on old client projects
{.is-info}

> **What will  you be doing:** Learning About Leverage
{.is-info}

> **Why you should be doing this:** Brolik Still has several old clients on Leverage
{.is-info}

> **Who should you seek help from if necessary:** Kyle Jasso (jassok@brolik.com or kyle.jasso@gmail.com) For questions. Subject line: `BRO - Leverage: Questions?`
{.is-info}

> **How long should this take:**
{.is-info}

-----
# Information
## What is it?
Leverage is a CMS built by Drew Thomas Back in 2012. It was the Primary CMS used by the company through 2017, with Fishtown Brew Pub Being the last client built upon it. It is a PHP based CMS built on a SASS and jQuery Framework. 

There are a few rules and practices that were put in place while developing sites based on Leverage, but for the most part anything goes when it comes to working in Leverage. It was designed to be a one-size-fits for any site, so the bulk of the work and tasks can be custom coded and overwritten. 

## Getting Started
To run any Leverage site, you need to have a web server running, with access to .htaccess. Typically, Leverage sites were worked on locally using MAMP, Compass with PHP 5.6.30.

* https://www.mamp.info/en/
* http://compass-style.org/
* https://downloads6.mamp.info/MAMP-PRO/releases/4.5/php5.6.30.zip

Once you have the software, you need to create a `local.settings.php` file inside of the `lib/` folder. There should be a sample file in there to work off of.


### local.settings.php
```php
<?php

// /* == Client Settings ======================================== */
// $_SITE['CLIENT_NAME']   = 'Powerhouse';
// $_SITE['CLIENT_LOGO']   = 'images/logo.jpg';
$_SITE['CLIENT_WEBSITE']   = 'http://localhost:8080';
$_SITE['CLIENT_EMAIL']     = 'yourEmail';

/* == Server Settings ======================================== */
$_SITE['DB_HOST']          = 'external database Url';
// $_SITE['DB_USER']       = 'database username';
// $_SITE['DB_PASS']       = 'database password';
// $_SITE['DB_NAME']       = 'database name';

$_SITE['LIVE']             = false;

?>
```

This File is designed to overwrite `settings.php` which defines the `<base>` tag, and database connection. Typically, the only thing you'll need to change will be `CLIENT_WEBSITE`, `CLIENT_EMAIL`, and `DB_HOST`. 99% of all Leverage sites were built on media temple, meanint the the `DB_HOST` variable in `settings.php` should look like this: `internal-db.s000000.gridserver.com`, so the `local.settings.php` setting should be: `external-db.s000000.gridserver.com`.

`CLIENT_WEBSITE` should be what ever your localhost url is. With Mamp, you can either point to the main repo directory, or a project directory. 

## Creating an admin file
There are technically two versions of an admin file. A normal, multi page file, and a single `?slide` file. A multi page file, requires a database table with an AutoIncrement primary key, while a single page file requires a database with one entry with a primary key value of `1`

Before creating an admin file, you'll need to create a database table for the page. This can typically be done through phpMyAdmin. As stated before. 99% of all Leverage sites are built upon MediaTemple, meaning you can access phpMyAdmin by going to `http://domain.com/.tools/phpMyAdmin` or `http://s000000.gridserver.com/.tools/phpMyAdmin` The  username and password can be found in `settings.php`

Once the database is created, you'll need to use one of the following two files:

### Dynamic, or Multi page Admin File

```php
<?php
include_once("database.php");
include_once("lib/functions.php");
$formSuffix = randomString();


/* ======================================================================

SETUP /
Follow the steps below to set up both a list of database entries and a form to update each entry (first time the file loads, it displays the list, then clicking on a list item brings up the form to update that item)
Lines ending with // x do not need to be changed... but feel free to anyway!

====================================================================== */

/* ----------------------------------------------------------------------
1 / Set the database table and the primary key field.
---------------------------------------------------------------------- */
$tableName = "";
$primaryKey = "";

/* ----------------------------------------------------------------------
2 / If entries can be manually sorted (drag and drop style), set the database field for an integer that holds each entries' order. Otherwise, set this to false.
---------------------------------------------------------------------- */
$sortField = false; // or false;

/* ----------------------------------------------------------------------
3 / To "gray out" inactive entries, set the database field for a boolean where 1 is "active" and 0 is "inactive." If there's no active/inactive field, set this to false.
---------------------------------------------------------------------- */
$activeField = false; // or false;
if (!$activeField) { $activeField_query = ""; } else { $activeField_query = ",".$activeField; } // x

/* ----------------------------------------------------------------------
4 / If entries shouldn't be deleted, set this to true.
---------------------------------------------------------------------- */
$noDelete = false; // or true;

/* ----------------------------------------------------------------------
5 / If entries can't be manually sorted, set the ORDER BY part of the mysql query for the list of entries.
---------------------------------------------------------------------- */
$orderby = "title ASC";
if ($sortField) { $orderby = $sortField." ASC"; } // x

/* ----------------------------------------------------------------------
6 / Set array values for the database field(s) used to display each entry.
---------------------------------------------------------------------- */
$displayby = array("title");

$displayby_query = implode(",",$displayby); // x
foreach($displayby as $key=>$value) { ${"displayby_".$key} = $value; } // x

/* ----------------------------------------------------------------------
7 / Set the section title and any special instructions for this section.
---------------------------------------------------------------------- */
$title = "";
$instructions = "";

/* ----------------------------------------------------------------------
8 / Set the file to submit the form to (via ajax). Most of the time, it's this filename.
---------------------------------------------------------------------- */
$ajaxFilename = "";

/* ----------------------------------------------------------------------
9 / Set the database fields that are in the form and should be updated when the form is submitted.
---------------------------------------------------------------------- */
$fields = array();
if ($sortField && in_array($sortField,$fields)) { unset($fields[$sortField]); } // x make sure $sortField is not in array
if ($activeField && !in_array($activeField,$fields)) { $fields[] = $activeField; } // x make sure $activeField is in array

/* ----------------------------------------------------------------------
NEXT / That's it for setup. Now skip down to the form fields, and create a form element for each database field that you want to update.
---------------------------------------------------------------------- */

/* ======================================================================

Form to Update a Single Entry /
First, we handle any submitted form data. By default, fields (set in the array above) are saved to the database table (also set above).
Next, we output the form. Customize it with the fields in your database table.

====================================================================== */
$addNew = false;
if (isset($_REQUEST['primaryKeyVal'])) {

    $primaryKeyVal = $_REQUEST['primaryKeyVal'];
    if ($primaryKeyVal == 0) { $addNew = true; }

    /* ----------------------------------------------------------------------
    Save / Here is where we save to the database. This is where you should do any custom handling of your data.
    ---------------------------------------------------------------------- */
    if (isset($_REQUEST['save']) || !$primaryKeyVal && !$addNew) {

        $values = array(); //- get all database fields as variables
        if($activeField && empty($_REQUEST[$activeField])) {
            $_REQUEST[$activeField] = 0;
        }

        if(isset($_REQUEST['button'])) {
            foreach($_REQUEST['button'] AS $key => $val) {
                $_REQUEST[$key.'_button'] = json_encode($val);
            }
        }
        
        foreach($fields AS $key => $value) {
            $values[$key] = $_REQUEST[$value];
        }

        // Save fields
        $addupdate = addUpdate($tableName,$primaryKey,$primaryKeyVal,$fields,$values,false); //---- write to the database (this adds and updates)
        $primaryKeyVal = $addupdate[$primaryKey]; // x get newly generated primaryKeyVal (in case we just added the entry)

        // Save an image
        if(isset($_FILES[''])) {
            $addImage = addImage($tableName,$primaryKey,$primaryKeyVal,"","images/uploads","original","","w");
        }

    }

    /* ----------------------------------------------------------------------
    Delete / Here is where we delete.
    ---------------------------------------------------------------------- */
    if (isset($_REQUEST['delete'])) {
        $delete = deleteByFieldsValues($tableName,$primaryKey,$primaryKeyVal);
    }

    if (isset($_REQUEST['removeImage'])) {
        $fieldName = $_REQUEST['fieldName'];
        $addupdate = addUpdate($tableName,$primaryKey,$primaryKeyVal,$fieldName,"",false);
    }

    ?>

    <?php
    /* ----------------------------------------------------------------------
    Random Set Up / Here we create a few variables and do some database queries that we'll use later. No need to mess with this section unless you want to.
    ---------------------------------------------------------------------- */
    $query = mysql_query("SELECT $primaryKey,$displayby_query$activeField_query FROM `$tableName` WHERE $primaryKey='$primaryKeyVal' LIMIT 1 ");
    $q = mysql_fetch_assoc($query);

    $listItemText = $q[$displayby_0];

    $listItemLiHref = $primaryKey."-".$q[$primaryKey]; // listItem parent li link (so that the ajax form works correctly)

    $mq = mysql_fetch_assoc(mysql_query("SELECT * FROM `$tableName` WHERE $primaryKey='$primaryKeyVal' LIMIT 1 ")); //---- get values to populate form

    ?>

    <script type="text/javascript">
    $(function() {

        <?php include("js/LEV_formJavascript.js"); ?> // include all the form javascript stuff. This needs to be a php include because it uses php variables from above.

        function formBeforeSerialize() {
            // this is called before the form serializes
        }

        function formSuccess() {
            // this is called after the form successfully submits
        }

    });
    </script>

    <?php
    /* ----------------------------------------------------------------------
    Set Up the HTML Around Each Form Element / This variable holds code that goes before, between and after form labels and inputs. Override it if you want custom styling.
    ---------------------------------------------------------------------- */
    $formHTML = array(
        'before'=>'<div class="oneField clearfix"><div class="three columns noMargin"><div class="leftSide">',
        'between'=>'</div></div><div class="twelve columns noMargin"><div class="rightSide">',
        'after'=>'</div></div></div>'
    );
    ?>

    <h1><?php echo $title; ?></h1>
    <h2><?php echo $instructions; ?></h2>

    <?php
    /* ----------------------------------------------------------------------
    The Form / Manually add all database fields that you want users to update to this form. This is purposely left open for easy customization, but example of common form elements are all here so it's easy to get up and running.
    ---------------------------------------------------------------------- */
    ?>
    <form id="addupdate_<?php echo $formSuffix; ?>" name="addupdate_<?php echo $formSuffix; ?>" method="post" action="<?php echo $ajaxFilename; ?>" enctype="multipart/form-data">

        <div class="actionButtons"><div class="rightButton" style="position:absolute; right:1%; top:1%;"><a href="#cancel"><button type="button" class="saveButton" style="font-style:normal; padding:10px 15px;">X</button></a></div></div>

        <input type="hidden" name="<?php echo $primaryKey; ?>" value="<?php echo $primaryKeyVal; ?>" />
        <input type="hidden" name="primaryKey" value="<?php echo $primaryKey; ?>" />
        <input type="hidden" name="primaryKeyVal" value="<?php echo $primaryKeyVal; ?>" />

        <!-- Fields -->

        

        <!-- Fields -->

        <!-- --------------/ Save, Cancel and Delete Buttons /------------------ -->
        <?php echo $formHTML['before']; ?>&nbsp;<?php echo $formHTML['between']; ?>
        <div class="actionButtons clearfix">
            <div class="leftButton"><button type="submit" name="save" value="Close" class="saveButton">Save / Close</button></div>
            <div class="leftButton"><button type="submit" name="save" value="Save">Save</button></div>
            <?php if (!$noDelete) { ?><div class="rightButton"><a href="#delete"><button type="button" class="deleteButton">Delete</button></a></div><?php } ?>
            <div class="rightButton"><a href="#cancel"><button type="button">Cancel</button></a></div>
        </div>
        <?php echo $formHTML['after']; ?>

    </form>

<?php } else {

/* ======================================================================

List of Entries / For the most part, the following won't need to be changed

====================================================================== */

    if (isset($_REQUEST['action']) && $_REQUEST['action'] == "saveOrder") {
        $saveOrder = saveOrder($tableName,$primaryKey,$sortField,$_REQUEST[$primaryKey]);
        if ($saveOrder) { echo "Saved"; } else { echo "There was an error. Please try again."; }
        die();
    }

    if ($sortField) { ?>
    <script type="text/javascript">
        $(function() {

            <?php
            if ($sortField) {
                include("js/LEV_sortJavascript.js"); // sortable options and actions. Needs to be a php include because it uses php variables.
            }
            ?>

        });
    </script>
    <?php }

    echo "<ul class='menuList' id='ul-".$tableName."' style='display:block;'>";


    echo "<li class='addNew' id='".$primaryKey."-0'>"; //---- add new
        echo "<div class='trigger ajaxContent addNew' data-ajaxurl='".$ajaxFilename."'><span class='icon add'></span> Add New</div>";
    echo "</li>";

    $query = mysql_query("SELECT $primaryKey,$displayby_query$activeField_query FROM `$tableName` ORDER BY $orderby ") or die(mysql_error());
    while ($q = mysql_fetch_assoc($query)) {

        echo "<li id='".$primaryKey."-".$q[$primaryKey]."'>";

            echo "<div class='icons listItem'>"; // icons
                if ($sortField) { echo "<span class='icon reorder'></span>"; } // reorder handle icon
                if (!$noDelete) { echo "<span class='icon delete'></span>"; } // delete icon
            echo "</div>";

            $activeClass = "";            
            if ($activeField && $q[$activeField]) { $activeClass = ""; } else if ($activeField) { $activeClass = "inactive"; } // "gray out" inactive classes

            echo "<div class='trigger ajaxContent $activeClass' data-ajaxurl='".$ajaxFilename."'>".$q[$displayby_0]."&nbsp;</div>";

        echo "</li>";

    }

    echo "</ul>";

    ?>

<?php }

mysql_close($conn);

?>
```

#### Lines to Edit
LN 18: `$tableName = "";` Name of the table you created
LN 19: `$primaryKey = "";` Column name of primary key
LN 24: `$sortField = false; // or false;` False or the column name of the sort field, typically "orderNum"
LN 29: `$activeField = false; // or false;` False, or the column name of the active field, typically "active"
LN 35: `$noDelete = false; // or true;` Prevents the user from deleting entries
LN 40: `$orderby = "title ASC";` Defailt ordering if no sort field
LN 46: `$displayby = array("title");` Display fields, can be multiple fields
LN 54: `$title = "";` Shows up on the page as the title
LN 55: `$instructions = "";` Shows up on the page as instructions for the user
LN 60: `$ajaxFilename = "";` the name of the file you're creating. (What you're saving it as)
LN 65: `$fields = array();` A list of all the column names, excluding images, that need to be saved.
LN 111: `if(isset($_FILES[''])) {` Set `$_FILES['']` to the input name of an image upload
LN 112: `$addImage = addImage($tableName,$primaryKey,$primaryKeyVal,"","images/uploads","original","","w");` The function takes: (table name, primary key name, primayr key value, asset field name, upload location, size, asset field name, restrict w or h)

Between lines 89 and 115 can be used for custom saving. If you need to do more then `PUT` or `INSERT` into the table, such as foregin key linking, do it here. You will need to write your own queries.

Lines 111-113 can be copied for multiple image uploads

Between Lines 189 and 193, add the fields that are necessary for the page.

#### Field Templates

```php
<!-- --------------/ Single Line Input /---------------- -->
<?php echo $formHTML['before']; ?>
    <label>Title</label>
<?php echo $formHTML['between']; ?>
    <input type="text" name="title" value="<?php echo $mq['title']; ?>" />
<?php echo $formHTML['after']; ?>
```


-----



```php
<!-- --------------/ Textarea /------------------ -->
<?php echo $formHTML['before']; ?>
    <label>blurb</label>
<?php echo $formHTML['between']; ?>
    <textarea name="blurb" class="tinyMCE"><?php echo $mq['blurb']; ?></textarea>
    <div class="hidden hint"><p>Pay attention to the FORMAT dropdown. Most of the time, you'll want to make sure to select PARAGRAPH.</p><p>If you're getting strange characters and formatting, and you're pasting from Microsoft Word, try pasting from another program or typing directly into the box.</p></div>
<?php echo $formHTML['after']; ?>
```


-----


```php
<!-- --------------/ Checkbox /------------ -->
<?php echo $formHTML['before']; ?>
    <label>Active?</label>
<?php echo $formHTML['between']; ?>
    <input type="checkbox" name="active" value="1" <?php if ($mq['active'] || $addNew) { echo "checked"; } ?> /><label>This will show up on the site</label>
<?php echo $formHTML['after']; ?>
```

-----


```php
<!-- --------------/ File Field /------------------ -->
<?php echo $formHTML['before']; ?>
    <label>Upload an Image</label>
<?php echo $formHTML['between']; ?>
        <?php if (is_file("../".$mq['image'])) { ?>
            <div class="half column noMarginLeft"><div style="border-right:1px dotted #ccc;"><img src="<?php echo "../".$mq['image']; ?>" style="max-height:200px;" /></div></div>
            <div class="half column noMarginRight">
                <p>Replace:<br /><input type="file" name="image" value="" /></p>
                <p><a data-fieldname='image' href="#remove">(x) Remove</a></p>
            </div>
        <?php } else { ?>
            <input type="file" name="image" value="" />
        <?php } ?>
<?php echo $formHTML['after']; ?>
```


-----


```php
<!-- --------------/ Foreign Key /------------------ -->
<?php echo $formHTML['before']; ?>
    <label>Category</label>
<?php echo $formHTML['between']; ?>
    <select name="categoryID">
        <option value="0">-</option>
        <?php
            $query = mysql_query("SELECT categoryID,name FROM examplecategories ORDER BY name ASC");
            while ($q = mysql_fetch_assoc($query)) {
                if ($mq['categoryID'] == $q['categoryID']) { $selected = "selected"; } else { $selected = ""; }
                echo "<option value='$q[categoryID]' $selected>$q[name]</option>";
            }
        ?>
    </select>
<?php echo $formHTML['after']; ?>
```
*Note, this is a 1:1 relationship*


-----


```php
<!-- --------------/ Datepicker /------------------ -->
<?php echo $formHTML['before']; ?>
    <label>Date</label>
<?php echo $formHTML['between']; ?>
    <input type="text" name="datevar" value="<?php if ($mq['datevar']>0) { echo $mq['datevar']; } else { echo date('Y-m-d'); } ?>" class="datepicker" />
<?php echo $formHTML['after']; ?>
```


### Single Page Admin

The sections you need to edit should be similar to the multi page admin, but the lines will be different.

```php
<?php
include_once("database.php");
include_once("lib/functions.php");
$formSuffix = randomString();


/* ======================================================================

SETUP /
Follow the steps below to set up both a list of database entries and a form to update each entry (first time the file loads, it displays the list, then clicking on a list item brings up the form to update that item)
Lines ending with // x do not need to be changed... but feel free to anyway!

====================================================================== */

/* ----------------------------------------------------------------------
1 / Set the database table and the primary key field.
---------------------------------------------------------------------- */
$tableName = "";
$primaryKey = "";

/* ----------------------------------------------------------------------
2 / If entries can be manually sorted (drag and drop style), set the database field for an integer that holds each entries' order. Otherwise, set this to false.
---------------------------------------------------------------------- */
$sortField = false; // or false;

/* ----------------------------------------------------------------------
3 / To "gray out" inactive entries, set the database field for a boolean where 1 is "active" and 0 is "inactive." If there's no active/inactive field, set this to false.
---------------------------------------------------------------------- */
$activeField = false; // or false;
if (!$activeField) { $activeField_query = ""; } else { $activeField_query = ",".$activeField; } // x

/* ----------------------------------------------------------------------
4 / If entries shouldn't be deleted, set this to true.
---------------------------------------------------------------------- */
$noDelete = true; // or true;

/* ----------------------------------------------------------------------
5 / If entries can't be manually sorted, set the ORDER BY part of the mysql query for the list of entries.
---------------------------------------------------------------------- */
$orderby = "title ASC";
if ($sortField) { $orderby = $sortField." ASC"; } // x

/* ----------------------------------------------------------------------
6 / Set array values for the database field(s) used to display each entry.
---------------------------------------------------------------------- */
$displayby = array("title");

$displayby_query = implode(",",$displayby); // x
foreach($displayby as $key=>$value) { ${"displayby_".$key} = $value; } // x

/* ----------------------------------------------------------------------
7 / Set the section title and any special instructions for this section.
---------------------------------------------------------------------- */
$title = "";
$instructions = "";

/* ----------------------------------------------------------------------
8 / Set the file to submit the form to (via ajax). Most of the time, it's this filename.
---------------------------------------------------------------------- */
$ajaxFilename = "";

/* ----------------------------------------------------------------------
9 / Set the database fields that are in the form and should be updated when the form is submitted.
---------------------------------------------------------------------- */
$fields = array();
if ($sortField && in_array($sortField,$fields)) { unset($fields[$sortField]); } // x make sure $sortField is not in array
if ($activeField && !in_array($activeField,$fields)) { $fields[] = $activeField; } // x make sure $activeField is in array

/* ----------------------------------------------------------------------
NEXT / That's it for setup. Now skip down to the form fields, and create a form element for each database field that you want to update.
---------------------------------------------------------------------- */

/* ======================================================================

Form to Update a Single Entry /
First, we handle any submitted form data. By default, fields (set in the array above) are saved to the database table (also set above).
Next, we output the form. Customize it with the fields in your database table.

====================================================================== */
$addNew = false;
$primaryKeyVal = 1;

    /* ----------------------------------------------------------------------
    Save / Here is where we save to the database. This is where you should do any custom handling of your data.
    ---------------------------------------------------------------------- */
    if (isset($_REQUEST['save']) || !$primaryKeyVal && !$addNew) {

        $values = array(); //- get all database fields as variables
        if($activeField && empty($_REQUEST[$activeField])) {
            $_REQUEST[$activeField] = 0;
        }
        
        if(isset($_REQUEST['button'])) {
            foreach($_REQUEST['button'] AS $key => $val) {
                $_REQUEST[$key.'_button'] = json_encode($val);
            }
        }

        foreach($fields AS $key => $value) {
            $values[$key] = $_REQUEST[$value];
        }

        // Save fields
        $addupdate = addUpdate($tableName,$primaryKey,$primaryKeyVal,$fields,$values,false); //---- write to the database (this adds and updates)
        $primaryKeyVal = $addupdate[$primaryKey]; // x get newly generated primaryKeyVal (in case we just added the entry)

        // Save an image
        if(isset($_FILES[''])) {
            $addImage = addImage($tableName,$primaryKey,$primaryKeyVal,"","images/uploads/","original","","w");
        }
    }

    /* ----------------------------------------------------------------------
    Delete / Here is where we delete.
    ---------------------------------------------------------------------- */
    if (isset($_REQUEST['delete'])) {
        $delete = deleteByFieldsValues($tableName,$primaryKey,$primaryKeyVal);
    }

    if (isset($_REQUEST['removeImage'])) {
        $fieldName = $_REQUEST['fieldName'];
        $addupdate = addUpdate($tableName,$primaryKey,$primaryKeyVal,$fieldName,"",false);
    }

    ?>

    <?php
    /* ----------------------------------------------------------------------
    Random Set Up / Here we create a few variables and do some database queries that we'll use later. No need to mess with this section unless you want to.
    ---------------------------------------------------------------------- */
    $query = mysql_query("SELECT $primaryKey,$displayby_query$activeField_query FROM `$tableName` WHERE $primaryKey='$primaryKeyVal' LIMIT 1 ");
    $q = mysql_fetch_assoc($query);

    $listItemText = $q[$displayby_0];

    $listItemLiHref = $primaryKey."-".$q[$primaryKey]; // listItem parent li link (so that the ajax form works correctly)

    $mq = mysql_fetch_assoc(mysql_query("SELECT * FROM `$tableName` WHERE $primaryKey='$primaryKeyVal' LIMIT 1 ")); //---- get values to populate form

    ?>

    <script type="text/javascript">
    $(function() {

        <?php include("js/LEV_formJavascript.js"); ?> // include all the form javascript stuff. This needs to be a php include because it uses php variables from above.

        function formBeforeSerialize() {
            // this is called before the form serializes
        }

        function formSuccess() {
            // this is called after the form successfully submits
        }

    });
    </script>

    <?php
    /* ----------------------------------------------------------------------
    Set Up the HTML Around Each Form Element / This variable holds code that goes before, between and after form labels and inputs. Override it if you want custom styling.
    ---------------------------------------------------------------------- */
    $formHTML = array(
        'before'=>'<div class="oneField clearfix"><div class="three columns noMargin"><div class="leftSide">',
        'between'=>'</div></div><div class="twelve columns noMargin"><div class="rightSide">',
        'after'=>'</div></div></div>'
    );
    ?>

    <h1><?php echo $title; ?></h1>
    <h2><?php echo $instructions; ?></h2>

    <?php
    /* ----------------------------------------------------------------------
    The Form / Manually add all database fields that you want users to update to this form. This is purposely left open for easy customization, but example of common form elements are all here so it's easy to get up and running.
    ---------------------------------------------------------------------- */
    ?>
    <form id="addupdate_<?php echo $formSuffix; ?>" name="addupdate_<?php echo $formSuffix; ?>" method="post" action="<?php echo $ajaxFilename; ?>" enctype="multipart/form-data">

        <div class="actionButtons"><div class="rightButton" style="position:absolute; right:1%; top:1%;"><a href="#cancel"><button type="button" class="saveButton" style="font-style:normal; padding:10px 15px;">X</button></a></div></div>

        <input type="hidden" name="<?php echo $primaryKey; ?>" value="<?php echo $primaryKeyVal; ?>" />
        <input type="hidden" name="primaryKey" value="<?php echo $primaryKey; ?>" />
        <input type="hidden" name="primaryKeyVal" value="<?php echo $primaryKeyVal; ?>" />

        <!-- Fields -->


        


        <!-- Fields -->

        <!-- --------------/ Save, Cancel and Delete Buttons /------------------ -->
        <?php echo $formHTML['before']; ?>&nbsp;<?php echo $formHTML['between']; ?>
        <div class="actionButtons clearfix">
            <div class="leftButton"><button type="submit" name="save" value="Close" class="saveButton">Save / Close</button></div>
            <div class="leftButton"><button type="submit" name="save" value="Save">Save</button></div>
            <?php if (!$noDelete) { ?><div class="rightButton"><a href="#delete"><button type="button" class="deleteButton">Delete</button></a></div><?php } ?>
            <div class="rightButton"><a href="#cancel"><button type="button">Cancel</button></a></div>
        </div>
        <?php echo $formHTML['after']; ?>

    </form>

<?php mysql_close($conn); ?>
```

### Adding it to the menu

Once you have created your file, you need to add it to `admin/index.php` this file contains a `$pageList` array with all the admin files in it.  Single page files need `?slideContent` attached to the end of them, while Dynamic admin pages are just the file name.

## Font-end pages
A blank front-end page should look like this:

```php
<?php $pagename = ""; ?>
<?php $additionalHeadTags = ""; // <link rel='canonical' href='' /> ?>
<?php include("header.php"); ?>

<?php include("footer.php"); ?>
```

`$pagename` gets added to the `<main>` tag as a class and can be used to target the page in the sass.

All data and content from the database that needs to be displayed needs to have its queries written manually. The projects will be a mix of `mysql_connect` and `mysqli`.

To get the Styles to complie, you will need to run `compass watch` from the command line after installing it.

## Troubleshooting
> If you get a "leverage install" or "cannot connect to the database" error, you will need to add your ip address to the MediaTemple whitelist
{.is-warning}
	1. Login into the clients MediaTemple Account
	2. Domain Admin
	3. Mange Databases
	4. Manage Users
	5. Add My IP


>  Styles Don't load, but the rest of the page shows up
{.is-warning}
	1. Make sure local.settings.php is there
	2. Check that the URL in `CLIENT_WEBSITE` matches the url in your browser
	3. Inspect element and look for the `<base href="">` tag in the header, does it match the url?

> Admin Files arent saving
{.is-warning}
1. Add php warnings and debug from there

```php
ini_set('display_errors',1);
ini_set('display_startup_errors',1);
error_reporting(-1);
```
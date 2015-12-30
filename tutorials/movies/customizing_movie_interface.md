
# Customizing Movie Interface


### Customizing Field Captions

In our movie grid and form, we have a field named *Runtime*. This field expects an integer number in *minutes*, but in its title there is no sign of this. Let's change its title to Runtime (mins).

There are several ways to do this. Our options include server side form definition, server side columns definition, from script grid code etc. But let's make this change in the central location, the entity itself, so its title changes everywhere.

When Sergen generated code for Movie table, it created a entity class named MovieRow. You can find it at  *MovieTutorial.Web/Modules/MovieDB/Movie/MovieRow.cs*.

Here is an excerpt from its source with our Runtime property:

```cs
namespace MovieTutorial.MovieDB.Entities
{
    // ...
    [ConnectionKey("Default"), DisplayName("Movie"), InstanceName("Movie"), 
     TwoLevelCached]
    public sealed class MovieRow : Row, IIdRow, INameRow
    {
        // ...
        [DisplayName("Runtime")]
        public Int32? Runtime
        {
            get { return Fields.Runtime[this]; }
            set { Fields.Runtime[this] = value; }
        }
        //...
    }
}
```

We'll talk about entities (or rows) later, let's now focus on our target and change its DisplayName attribute value to *Runtime (mins)":

```cs
namespace MovieTutorial.MovieDB.Entities
{
    // ...
    [ConnectionKey("Default"), DisplayName("Movie"), InstanceName("Movie"), 
     TwoLevelCached]
    public sealed class MovieRow : Row, IIdRow, INameRow
    {
        // ...
        [DisplayName("Runtime (mins)")]
        public Int32? Runtime
        {
            get { return Fields.Runtime[this]; }
            set { Fields.Runtime[this] = value; }
        }
        //...
    }
}
```

Now build solution and run application. You'll see that field title is changed in both grid and dialog. 

> Column title has "..." in it as column is not wide enough, though its hint shows the full title. We'll see how to handle this soon.

![Movies Runtime (Mins)](img/movies_runtime_mins.png)


### Overriding Column Title and Width 


So far so good, what if we wanted to show another title in grid (columns) or dialog (form). We can override it corresponding definition file.

Let's do it on columns first. Next to *MovieRow.cs*, you can find a source file named *MovieColumns.cs*:

```cs
namespace MovieTutorial.MovieDB.Columns
{
    // ...

    [ColumnsScript("MovieDB.Movie")]
    [BasedOnRow(typeof(Entities.MovieRow))]
    public class MovieColumns
    {
        [EditLink, DisplayName("Db.Shared.RecordId"), AlignRight]
        public Int32 MovieId { get; set; }
        //...
        public Int32 Runtime { get; set; }
    }
}
```

You may notice that this columns definition is based on the Movie entity (BasedOnRow attribute).

Any attribute written here will override attributes defined in the entity class.

Let's add a DisplayName attribute to the *Runtime* property:

```cs
namespace MovieTutorial.MovieDB.Columns
{
    // ...

    [ColumnsScript("MovieDB.Movie")]
    [BasedOnRow(typeof(Entities.MovieRow))]
    public class MovieColumns
    {
        [EditLink, DisplayName("Db.Shared.RecordId"), AlignRight]
        public Int32 MovieId { get; set; }
        //...
        [DisplayName("Runtime in Minutes"), Width(150), AlignRight]
        public Int32 Runtime { get; set; }
    }
}
```

Now we set column caption to "Runtime in Minutes". 

We actually added two more attributes. 

One to override column width to 150px.

> Serenity applies an automatic width to columns based on field type and character length, unless you set the width explicitly.

And another one to align column to right (AlignCenter, AlignLeft is also available).

Let's build and run again, than we get: 

![Movies Runtime Column](img/movies_runtime_column.png)

Form field title stayed same, while column title changed.

> If we wanted to override form field title instead, we would do similar steps in MovieForm.cs


### Changing Editor Type For Description and Storyline

Description and Storyline fields can be a bit longer compared to Title field, so lets change their editor types to a textarea.

Open *MovieForm.cs* in the same folder with *MovieColumns.cs* and *MovieRow.cs*.

```cs
namespace MovieTutorial.MovieDB.Forms
{
    //...
    [FormScript("MovieDB.Movie")]
    [BasedOnRow(typeof(Entities.MovieRow))]
    public class MovieForm
    {
        public String Title { get; set; }
        public String Description { get; set; }
        public String Storyline { get; set; }
        public Int32 Year { get; set; }
        public DateTime ReleaseDate { get; set; }
        public Int32 Runtime { get; set; }
    }
}
```

and add TextAreaEditor attributes to both:

```cs
namespace MovieTutorial.MovieDB.Forms
{
    //...
    [FormScript("MovieDB.Movie")]
    [BasedOnRow(typeof(Entities.MovieRow))]
    public class MovieForm
    {
        public String Title { get; set; }
        [TextAreaEditor(Rows = 3)]
        public String Description { get; set; }
        [TextAreaEditor(Rows = 8)]
        public String Storyline { get; set; }
        public Int32 Year { get; set; }
        public DateTime ReleaseDate { get; set; }
        public Int32 Runtime { get; set; }
    }
}
```

I left more editing rows for Storyline (8) compared to Description (3) as Storyline should be much longer.

After rebuild and run, we have this:

![Movies TextArea Editors](img/movies_textarea_editors.png)

Serene has several editor types to choose from. Some are automatically picked based on field data type, while you need to explicitly set others.

> You can also develop your own editor types. You can take existing editor types as base classes, or develop your own from stratch. We'll see how in following chapters.

As editors became a bit higher, form height exceeded the default Serenity form height (which is set by Sergen to 260px) and now we have a vertical scroll bar. Let's remove it.

### Setting Initial Dialog Size With CSS (Less)

Sergen generated some CSS for our movie dialog in *MovieTutorial.Web/Content/site/site.less* file.

If you open it, and scroll to bottom, you will see this:

```cs
/* ------------------------------------------------------------------------- */
/* APPENDED BY CODE GENERATOR, MOVE TO CORRECT PLACE AND REMOVE THIS COMMENT */
/* ------------------------------------------------------------------------- */

.s-MovieDialog {
    > .size { .widthAndMin(650px); }
    .dialog-styles(@h: auto, @l: 150px, @e: 400px);
    .s-PropertyGrid .categories { height: 260px; }
}
```

You can safely remove the 3 comment lines (appended by code generator...). This is just a reminder for you to move them to a better place like a *site.movies.less* file specific to this module (recommended).

These rules are applied to elements with *.s-MovieDialog* class. Our Movie dialog has this class by default. 

In the second line it is specified that this dialog is 650px wide (and also its minimum width is 650px, this will get some meaning after we make this dialog resizable).

In third line, we specify that dialog height should be automatic (@h: auto), field labels should be 150px (@l: 150px) and editors should be 400px in width (@e: 400px).

We are using several less mixins here (widthAndMin and dialog-styles).

Our form height is controlled by *s-PropertyGrid .categories { height: 260px; }* line. Let's change it to 400px so it won't require a vertical scroll bar.

```css
.s-MovieDialog {
    > .size { .widthAndMin(650px); }
    .dialog-styles(@h: auto, @l: 150px, @e: 400px);
    .s-PropertyGrid .categories { height: 400px; }
}
```


### Changing Page Title

Our page has title of *Movie*. Let's change it to Movies. 

Open *MovieRow.cs* again. 

```cs
namespace MovieTutorial.MovieDB.Entities
{
    // ...
    [ConnectionKey("Default"), DisplayName("Movie"), InstanceName("Movie"), 
     TwoLevelCached]
    public sealed class MovieRow : Row, IIdRow, INameRow
    {
        [DisplayName("Movie Id"), Identity]
        public Int32? MovieId
```

Change DisplayName attribute value to *Movies*. This is the name that is used when this table is referenced, and it is usually a plural name. This attribute is used for determining default page title. 

> It is also possible to override the page title in *MoviePage.Index.cshtml* file but as before, we prefer to do it from a central location so that this information can be reused in other places.

InstanceName corresponds to singular name and is used in New Record (New Movie) button of the grid and determines the dialog title (e.g. Edit Movie).

```cs
namespace MovieTutorial.MovieDB.Entities
{
    // ...
    [ConnectionKey("Default"), DisplayName("Movies"), InstanceName("Movie"), 
     TwoLevelCached]
    public sealed class MovieRow : Row, IIdRow, INameRow
    {
        [DisplayName("Movie Id"), Identity]
        public Int32? MovieId
```

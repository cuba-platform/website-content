
## Maps in CUBA Applications

Hi everyone, 

In this video I’ll show you how to enrich your CUBA application with visual representation for spatial data using the Maps add-on.
Working with spatial data and displaying maps are essential for many business applications. 
But quite often Java developers face the challenge of adding maps to their web applications.  In most cases, it requires a developer to be familiar with front-end technologies (at the least, JavaScript). 
Even so, the implementation still may turn out to be complicated, full of boilerplate code and highly time-consuming.

The Maps add-on offers you an easy way to solve a wide range of tasks related to spatial data.
It integrates spatial types from JTS Topology Suite - the most popular Java library for working with spatial data.
It allows you to work with any map vendor compatible with the Web Map Service protocol or providing tiles in the XYZ format.
The add-on comes with a UI component with rich and clear API for building well-structured maps of any content.
It provides interactive tools for drawing and editing geo-objects and features for geoanalysis: heatmaps and clustering.

## Maps Structure

Multi-layer Structure.

The component supports traditional multi-layer structure commonly used in professional GIS systems. Layers can be of raster and vector types.
In this picture you can see the example of combining multiple layers to build a complete map. 

The bottom raster layer serves as a map’s background. It can be a TIle layer displaying tiles provided by the so-called XYZ tile services (for example, OpenStreetMap), or it can be a WMS layer displaying images provided by WebMapServices.

The next layers are Vector layers. They consist of geo-objects which are entities with geometry attributes. In our case the layer has Districts represented by geo polygons and Customers with locations defined by geo points.

## Demo Application

Now I’ll show you how to build a simple CUBA application with maps in 15 minutes.

Probably, some of you have already seen our petclinic demo project that can be found on the CUBA platform website. It demonstrates an IT system of an imaginary pokemon’s clinic with pre-filled data for clients, pets, vets and visits.

Let’s use this app as a starter and extend it with the maps .
First, take a look at its original data model.

Owner is an entity that represents the personal data of pet owners.
Pet contains name and birth date and is associated with the Owner and PetType classes
Visit details are contained in the Visit class and are related to one pet.
Vet is an entity containing veterinarian names extends Person and is associated with the Specialty class.

We will extend the data model in the following way:
First, let’s add a new entity - Clinic. This entity represents a branch clinic office and has only 2 attributes: name and location. The location attribute is of JTS point type and represents the physical location of a clinic.
Another new entity is District, which represents an area of a city defined by polygon geometry.
Next, let’s also add the location attribute to the Owner entity as well as a reference to a district. 
Finally, we will add a reference to a Clinic in the Visit entity. When a new visit is being created, the actual branch clinic office will be chosen based on the proximity to the pet’s owner location.

What we’re going to do is add maps to the editor screens for Clinics, Owners and Districts to enable interactive editing of the corresponding geometry. 
Also we will implement an algorithm that finds the nearest clinic for a client when they create a visit.
In the Owner browse screen we will add a map to display the owners with clustering mechanism enabled. 
Finally, we will create a heat map to visualize the overall information about the frequency of visits in different areas of the city.

Now we are ready to start developing our demo application.

Let’s start by cloning the petclinic github repository.
Open it in Cuba Studio.
We need to add the Maps add-on to our project. 
Open the Add-ons tab and go to Marketplace. Find the Maps add-on there and install it.
If you don’t have a subscription, you can get a trial version of the Maps add-on on the add-on’s page at the CUBA Platform website. 

## Extending Data Model

Before we start adding maps to our application screens, we need to prepare the data model.

Let’s create the Clinic entity.
Add 2 properties to it: name (of String type) and location (of GeoPoint type).
Navigate to the `Text` tab to see the class source code.
We need to add a couple of additional annotations to the location attribute.
The first one is the @Geometry annotation. It marks the attribute to be used when displaying the object on a map.
Another annotation is @Convert from javax.persistence package. In this annotation we will set the JPA converter which is needed to perform type conversion between the database and Java representation of the attribute. 
We will use the CubaPointWKTConverter which comes out-of-the-box. It transforms a point to a Well-known text format string and vice versa.
Let’s add the location property to the Owner entity in the same manner.
Now let’s create the District entity.
Add 2 properties to it: name (of String type) and polygon (of GeoPolygon type).

Navigate to the `Text` tab to see the class source code. 

Again, we need to add annotations to the property that represents the geometry, in this case - polygon: @Geometry annotation and @Convert annotation. We will use CubaPolygonWKTConverter as a converter.
Let’s add a reference to the Clinic in the Visit entity.

Next, we need to create a view for the Visit entity that includes this attribute. Right click on the Visit entity and create a view.
Name it visit_with_pet_and_clinic. Set visit_with_pet as a base view. Add the clinic attribute and its location to the view.
And finally, let’s add a reference to District in the Owner entity.

And create a view that contains this reference.
Name it owner_with_district. Add the district attribute and its polygon to the view.
We have made all necessary changes in the data model so it’s time to create the database.
Go to CUBA -> Generate database scripts.
Click Continue to confirm that existing scripts will be overwritten.
Create the database.
Now we are ready to create screens for the Clinic and District entities.
Right click on the Clinic and create browser and editor screens.

Repeat the same steps with the District entity.
After we have created screens, we can run the server to see if the things work properly.

We can see that the newly created screens are listed in the menu.

## Adding Maps to the Screens

Now we can go on to adding maps to the screens.
Let’s add a map to the Clinic editor screen so that a user can specify the location of a clinic right on the map.
Open an xml-descriptor of the Clinic editor screen.
Delete the locationField as we don’t need it.

Add the geoMap UI component under the form.
Press alt and enter to add the add-on’s namespace.
Specify the main parameters such as: width, height, zoom and coordinates of the map’s center. The center is specified by comma-separated double values of longitude and latitude. 
We will be using Madrid as a city for our demo application, so we will specify the coordinates roughly corresponding to Madrid’s center.

After that you need to declare the layers.
Our map will only have 2 layers: a tile layer as a background and a vector layer containing the current clinic.
We will be using the OpenStreetMap tile service for our demo, but you can use any other tile service by specifying a url-pattern and an attribution string, which some of the services require to add to a map. 
You can find the detailed information about tile services and WebMapServices usage in the add-on’s documentation.

Pass the clinicDc data container to the clinicLayer and make the layer editable to enable interactive editing of the clinic’s location.

Make sure to set clinicLayer as selectedLayer of the map.

Go back to the application. Create a new Clinic.
We can see that the map appeared in the screen. 
Since the clinic’s location is not specified yet, the map is opened in the drawing mode (we can see it from the special crosshair cursor). Click on the map to draw a point. 
After the point is drawn you can drag it,
or remove it by choosing the `Clear geometry` option in the context menu.

Similarly, let’s add a map to the District editor screen.

Delete the polygonField.
Add the geoMap UI component under the form.
Press alt and enter to add the add-on’s namespace.

Most of the parameters will be the same as in the Clinic editor screen.

Add a vector layer with districtLayer id. Pass the districtDc data container to the layer. Make the layer editable. 
Set it as selectedLayer of the map.

Let’s go back to the application to see the results. Create a new district.
We can see a map added to the screen. Click on the map to start drawing a polygon and keep on drawing until you get the desired geometry. 
Click on the last added point to finish drawing. You can edit the polygon by dragging its points. It is also possible to add a hole inside a polygon. 
Choose the ‘Add hole’ option in the context menu. Then, click on a polygon to start drawing a hole. 
Click on the last added point to finish. If you want to clear the polygon, choose `Clear geometry` in the context menu.
All changes in the geometry will be automatically saved after commit.

Create 2 districts as we will need them later. 
You can see that the table displays the text representation of polygons. 
This column was added automatically as the screen was created. Since this representation doesn’t say much, we can delete this column from the table. 
Go to the District browse screen and delete the column.

Now, let’s add a map to the Owner editor screen.

Switch to the Designer tab.
There are many fields listed here, so it will be better to add a map on the right. To accomplish this, we need to add an hbox to the screen and put fieldGroup and geoMap there.

Add an hbox under the layout. Add fieldGroup to the hbox.

Remember that earlier we added a reference to a district in the Owner entity. Now we need to add the corresponding property to the fieldGroup. 
But first, change the view of the ownerDc data container to owner-with-district in order to load the related district along with the owner.
Then, click on the column,then PROPERTIES -> Add field.
Add district property.

We will also need a data container for the district in order to use it in the vector layer. Switch to the PALETTE tab, click on the Data components, choose Instance container and put it under the ownerDc container. 
Switch to the PROPERTIES tab and choose the district property. Generate the id by clicking the button next to the corresponding field.
 
Go back to the XML. Add geoMap component to the hbox.
Set the main parameters: width, height, zoom, center.
Next, describe the layers. The first one will be a tile layer.
The next one is districtLayer - a vector layer that displays the related district specified in the corresponding field. 
The layer will be refreshed as the value in the field is changed.
The ownerLayer contains the editable owner instance, so pass the ownerDc datacontainer to the layer. 
Make the layer editable and set it as selectedLayer of the map.

Go back to the application. Create a new Owner.
Click on the map to specify the owner’s location.
Choose a district in the picker field. Notice that the corresponding layer has been refreshed as we updated the value. 
Try choosing another district or clear the value.
Up to now, we’ve been building maps only in XML descriptors. But you can also work with the geoMap UI component and layers programmatically in screen controllers. 
For example, in a screen controller we can customize styles for a point and a polygon. We can also add validation to check whether the owner’s location belongs to the district.

Let’s start with the styles. Go to the OwnerEdit controller.
To customize geometry styles you need to set a style provider function to a vector layer. This function calculates the style for a given geo object belonging to a layer.
Cuba 7 introduced a very handy mechanism that allows you to specify such functions declaratively, with no need to call a particular setter method.
For example, to define the style provider for the ownerLayer, declare setOwnerLayerStyleProvider method that returns GeometryStyle and takes an Owner as an argument. 
The name of the method is not important, but it should explain what the method is needed for. 
Add @Install annotation to the method. In “to” parameter specify the id of the geoMap UI component - map, in our case, then dot, and then the ID of the layer, ownerLayer, in our case. 
In the subject parameter set - styleProvider.
In the method’s body describe a style. In our case, just return a PointStyle with a custom icon.

Let’s do the same with the districtLayer. Now we need our method to return a PolygonStyle.

And finally let’s add validation to check if the owner’s location is inside the district. 
Subscribe to the preCommit event. 
Implement the validation in the method body. First, check that the location and district are not null. 
To check whether a point is within a polygon, use within() method. And if the condition fails, prevent commit and build a notification with a corresponding message.

Go back to the application, open the Owner editor. You can see that the style settings were applied to the layers. 
And if we set the location to be outside the district, we will witness an error message.

## Filling the application with data

Before we continue, we need to fill our application with data. Follow the link in the description below. 
These are 2 groovy scripts with predefined generated data that can be imported into your application. Download them. 

Go back to the application and open the JMX Console screen. 
Find the Scripting Manager bean by typing its name in the search field. 
In the RootPath property you can see the path to the directory in the application server where you should put the groovy scripts. Copy them to this directory.

Run “import1.groovy” script. As the message says, the import finished successfully. Do the same with the “import2.groovy” script.
Now that our application has a sufficient amount of data, we are able to proceed further.

## Setting the nearest clinic

Let’s move to the next task. When creating a Visit, we want to automatically set the nearest Clinic to the pet’s owner. 
To implement this, we will use the power of JTS framework, which provides a wide variety of functions and algorithms for vector geometry.

Go to the Visit editor screen.
Change the view of the visitDc data container to visit-with-pet-and-clinic. 
Also, change the view of the petsDc data container to pet-with-owner-and-type.
Add a clinic field to the field group.
Next, create a data container that will be loading the list of clinics.

Go to the screen controller.
Subscribe to the event of changing the selected Pet for the Visit.
Inject the clinics data container and the clinic field.
In the method body we will first check if the pet's Owner location is specified. 
If yes, then we will iterate through all available clinics to find the nearest one for the pet's owner. Otherwise, the Clinic will have to be chosen manually by a user. 
We will use the static method distance from DistanceOp class provided by JTS. This method calculates the distance between 2 points.

Go back to the application and create a new Visit. When we choose a Pet for the Visit, we can see that the Clinic is determined automatically based on the algorithm that we’ve just implemented.

## Clustering

The Maps add-on provides useful tools for geoanalysis: clustering and heatmaps. Let’s use these features in our demo-application. 
We will start with the clustering.
When opening the Owner browse screen, it would be handy to see not just a table of Owners but also the locations of the Owners on a map. 
We can also add an option to show the Owners belonging to a particular District.

Go back to the Studio and open the Owner browse screen. Switch to the Designer tab. 
We will reorganize the layout of the screen to place the map on the right. Add an hbox with 2 vboxes on the layout. 
Correct the size parameters of the hbox and vbox-es.
Move filter and ownersTable to the first vbox. Add a lookupField to the second vbox. Specify an id: districtField and a caption: “District”. 
We need to specify an optionsContainer which contains all available values for the field. To do this, first add a new collection container to the screen. 
Choose class: District. Then set this container to the districtField.

Go to the xml-descriptor and add a map under the districtField. Set width: 100% and set the expand parameter in the v box to: “map”. 
Add a tile layer and a vector layer of Owners.

Go back to the application to see the results.

So, that’s how it looks when the clustering is disabled. There are too many points on the map, which not only looks untidy but also leads to a bad performance.
To enable clustering, add a cluster tag inside the vector tag. In the cluster tag you can also specify some additional options, but in our case we just keep them default.

The next thing that we are going to implement is filtering by district. Subscribe to the changes in the districtField.
Inject owners data loader. Create a load context to apply an additional condition: to load only those owners who belong to the specified district.
Use dataManager to load entities from the database.
We also want to see the borders of the selected district on a map. We can achieve this by adding the district’s polygon to the map’s canvas. 
Canvas is a utility layer of the geoMap UI component providing a straightforward API for adding and drawing geometries on a map. 
It can be really useful when you need to display vector geometry on a map but you don’t want to work with entities that vector layers are working with.
You can obtain the Canvas by calling the getCanvas method of the GeoMap UI component.
Create a variable that will store the currently displayed polygon. Use this variable to remove the polygon from the canvas when the selected district is changed.
And finally let’s make it possible to choose the owner on the map and make the table automatically select the row with the chosen owner.
Create a method that takes the GeoObjectSelectedEvent as an argument and subscribe to the owners layer’s events.
In the method body perform selection of the needed item in the table.

Go back to the application to see the results. Now we can see that the owners are grouped into clusters. 
Select some District to see the owners belonging to the district. Click on some owner and notice that it is selected in the table as well.

## Heatmap

Now we can go on to the final task - creating a heatmap of Visits. Actually, this map will contain locations of the pet’s owners, but the intensity of each point will vary depending on how many visits the owner had. 
So this map is supposed to give the information which areas of the city suit best for opening a new branch clinic office.

Go back to the Studio and create a new blank screen in the visit directory and name it: Visits Heatmap.
Switch to the xml-descriptor and add the geoMap visual component to the screen. Set width and height 100% to make the map full-screen.
For the time being, we will add only one layer with OpenStreetMap tiles.
 
Switch to the designer. We will need 2 data containers: for clinics and visits. The visits should be loaded along with the pet’s owner, so we need to declare a new view. 
Let’s name it: visits-with-pet-and-owner.
Return to the xml-descriptor and add a new vector layer with clinics. Let’s customize the appearance of the clinic points. Switch to the screen controller and add the method similar to the one we created earlier in the OwnerEditor screen. Specify the HOSPITAL icon for the point and make it red.

Now we are ready to add a heatmap. Subscribe to the BeforeShow event of the screen. Load the data into the data containers by calling the getScreenData().loadAll method. 
First we need to build the java map of pairs: point and its intensity value of Double type. Inject the visits data container and build the intensityMap taking advantage of java streams. 
Inject the geoMap UI component and add the intensityMap to it by calling the addHeatMap method. 
You can also set additional options for the heatmap by instantiating the HeatMapOptions class, specifying the needed parameters and passing this object to the method along with the intensityMap.
 
Restart the application to apply the newly created screen.

Go to the Visits Heatmap screen to see the heatmap. If we were thinking about opening a new clinic we would find this visual information really useful.

## Conclusion

Allright, we’ve done everything we planned.

Join our community and feel free to ask questions.

Check out other add-ons at CUBA marketplace.

Consult the documentation on our website and on the add-on home page.

Star us on GitHub, subscribe to our Twitter and YouTube channel!

All links are clickable under the video.

Thank you for watching!
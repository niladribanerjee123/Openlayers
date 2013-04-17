Openlayers, Javascript code for feature identictaion.  
================================================================================================================

<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="Featureidentyfication._Default" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" >
 <head>
        <link rel="stylesheet" href="/stylesheets/style.css" type="text/css">
       <style>
           #map-id {
               width: 800px;
               height: 600px;
           }
       </style>
     
       <script src="javascript/OpenLayers.js" type="text/javascript">
                    
       </script>
   </head>


   <body>
      <link href="stylesheets/style.css" rel="stylesheet" type="text/css" />
       <h1>My Map</h1>
       <div id="map-id">
       
                  <button type="button" onclick="doPan()" >Pan</button>
                  <button type="button" onclick="identify()">Identify</button>
                         </div>
       <div id="get"></div>
       <script type="text/jscript">

          var bounds = new OpenLayers.Bounds(
                    -46.41712236881722, 36.383012520139054,    // Declaring the bounding box
                    46.95707446162123, 82.46342353857571
                );

          var options = {
               controls: [],
               maxExtent: bounds,                                     
               maxResolution: 0.3647429563689002,             //setting up map unit, map resolution
               projection: "EPSG:32643",
               units: 'm'
           };

           var map = new OpenLayers.Map("map-id", options);
          
           var ele_ntwrk = new OpenLayers.Layer.WMS(
               "Electrical_asset",
               "http://localhost:8080/geoserver/Electricalasset/wms",
               { layers: "Network1" }
           );
             
               map.addLayer(ele_ntwrk);

               // build up all controls
               map.addControl(new OpenLayers.Control.PanZoomBar({
                   position: new OpenLayers.Pixel(2, 15)
               }));
               map.addControl(new OpenLayers.Control.Navigation());
               map.addControl(new OpenLayers.Control.Scale($('scale')));
               map.addControl(new OpenLayers.Control.MousePosition({ element: $('location') }));
            
               map.zoomToMaxExtent(bounds);


               // set the protocol for the featureinfo request    

              var info = new OpenLayers.Control.WMSGetFeatureInfo({
               url: 'http://localhost:8080/geoserver/Electricalasset/wms',
               title: 'Identify features by clicking',
               queryVisible: true,
               INFO_FORMAT: 'application/vnd.ogc.gml',             
               buffer: 100,
               drillDown: true,
               queryVisible: true,
               QUERY_LAYERS: "Network1",
               output: "features"


           });


           function identify() {
     //          debugger;
               document.getElementById("map-id").style.cursor = 'pointer';

               info.events.register("getfeatureinfo", this, getFID);         //Registering click event
               map.addControl(info);
               info.activate();
           }
           function doPan() {
               info.events.unregister("getfeatureinfo", this, getFID);
               map.removeControl(info);
               info.deactivate();
               document.getElementById("map-id").style.cursor = 'default';
           }
            
           function getFID(e) {
           
               document.getElementById("divInfo").innerHTML = e.text;

               var ths = document.getElementById("divInfo").getElementsByTagName("th");
               var tds = document.getElementById("divInfo").getElementsByTagName("td");
               var fid = "";
           
               //Get FID
               for (var j = 0; j < ths.length; j++)
                {
                    if (ths[j].innerHTML == "fid")
                    {
                       fid = tds[j].innerHTML;
                    }
                   else
                    {
                       ths[j].style.display = "none";
                    }
               }

               alert("FID:  " + fid);
           
           }
                     
       </script>
       
       <div id = "divInfo" 
           style="width:auto; height:auto; top:400px; position:absolute; color: #FFFFFF;"></div>
   </body>

</html>





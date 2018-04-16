// This function gets NDVI from Landsat 5 imagery.

var getNDVI = function(image) {
  return image.normalizedDifference(['B4', 'B3']);
};
var roi = ee.Geometry.Point(69.14794921875,34.54049998801135);
// Load two Landsat 5 images, 20 years apart.
var image = ee.ImageCollection("LANDSAT/LT05/C01/T1_TOA");
var image1 = image.filterBounds(roi).filterDate('1990-06-03', '1990-06-05');
var image2 = image.filterBounds(roi).filterDate('2010-06-10', '1990-06-12');
// Compute NDVI from the scenes.
var ndvi1 = getNDVI(image1);
var ndvi2 = getNDVI(image2);

// Compute the difference in NDVI.
var ndviDifference = ndvi2.subtract(ndvi1);
// Load the land mask from the SRTM DEM.
var landMask = ee.Image('CGIAR/SRTM90_V4').mask();

// Update the NDVI difference mask with the land mask.
var maskedDifference = ndviDifference.updateMask(landMask);

// Display the masked result.
var vizParams = {min: -0.5, max: 0.5, palette: ['FF0000', 'FFFFFF', '0000FF']};
Map.setCenter(-122.2531, 37.6295, 9);
Map.addLayer(maskedDifference, vizParams, 'NDVI difference');

      

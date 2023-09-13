# GEEBasic_Javascript

✅ Tutorial 1
✔ Sorting Sentinel Image dataset and sorting it

In this tutorial for Google Earth Engine using JavaScript, we will learn how to sort the Sentinel Image dataset. We will also cover the process of sorting it.

// Add your study area shapefile and center it in the map viewing window
Map.centerObject(roi,13);
Map.addLayer(roi)

// Add a cloud mask
function maskS2clouds(image) {
  var qa = image.select('QA60');

  // Bits 10 and 11 are clouds and cirrus, respectively.
  var cloudBitMask = 1 << 10;
  var cirrusBitMask = 1 << 11;

  // Both flags should be set to zero, indicating clear conditions.
  var mask = qa.bitwiseAnd(cloudBitMask).eq(0)
      .and(qa.bitwiseAnd(cirrusBitMask).eq(0));

  return image.updateMask(mask).divide(10000);
}

// Image dataset sorting
var Dataset = ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED')
                  // Sort according to study area
                  .filterBounds(roi)
                  // Sort according to date
                  .filterDate('2022-03-01', '2022-09-30')
                  // Sort according to cloud percentage
                  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE',50))
                  // Apply the cloud mask function of the sorted image datasets
                  .map(maskS2clouds)
// Lets see the details of the sorted image dataset in the "Console" window
print(Dataset,'Dataset')

// Lets reduce the image collection in median
var Dataset = Dataset
             .reduce(ee.Reducer.median())
             .clip(roi);
// Let's check out the reduced dataset in the "Console"
print(Dataset ,'Dataset ');

// To display the image we will be needing a visualization parameter to create the RGB image. So, let's create one.
var visualization = {
  min: 0.0,
  max: 0.4,
  bands: ['B4_median', 'B3_median', 'B2_median'],
};

// Now let's display the image in the Map Window.
Map.addLayer(Dataset, visualization, 'RGB Median Image');

# FME® DegreeFormatter

## About  
Custom transformer for [FME®](https://www.safe.com/how-it-works/) that converts DMS-, DDM-, or ISO 6709-formatted coordinate values to DD-formatted values and vice versa.  
This transformer is able to convert multiple attributes of the same format and coordinate type at once. Values can contain either a latitude or longitude coordinate or both (as a delimited string with any kind of separator). 

### Example  
If an input attribute _MyAttribute_ holds a lat/lon DDM (Degrees + Decimal Minutes) coordinate string, e.g. 5°13.02'S 80°37.82'W, the resulting output feature will have the following attributes after it is converted to DD (Decimal Degrees), depending on the output settings:

- _MyAttribute = 5°13.02'S 80°37.82'W_  
- _MyAttribute.lat = 5.2169_  
- _MyAttribute.lon = -80.6303_  
- _MyAttribute.latlon = 5.2169° S, 80.6303° W_

In other words, the original _MyAttribute_ will be preserved and the output will be written to _.lat_, _.lon_ and _.latlon_ suffixed attributes. In the case of DD only, the _.lat_ and _.lon_ attributes will always be numeric.

### Error handling  
This transformer does not have a _<Rejected>_ port, because it allows multiple input attributes.  
However, if an input attribute did not match the specified input format or some other error occurred, the suffixed output attributes mentioned in the example above will not be set. Instead, the output attribute _MyAttribute.error_ should contain the failure reason (e.g. a validation error).  
Besides validation of the selected input format, the DegreeFormatter will also check if the coordinate(s) are within a valid range. Minutes and seconds should be smaller than 60. Latitudinal degrees should not be lower than -90 or higher than 90, whereas longitudinal degrees should not be lower than -180 or higher than 180. Note that if the _Input Type_ is "Latitude and Longitude", the DegreeFormatter assumes that the first value is the Latitude component and the second value is the Longitude component.

### Notes  
- Also available on [FME Hub](https://hub.safe.com/transformers/degreeformatter) for convenient installation.
- This transformer has been tested on Python 2.7 and 3.4.  
- Any non-UTF8 encoded string inputs will result in a UTF8 encoded output.   
- If you notice a bug or desire a new feature, please contact me. Or make a pull request!  
- Released under [GNU General Public License v3.0](https://github.com/SanderSchaminee/fme-degreeformatter/blob/master/LICENSE).
- The [TestSuite workspace](https://github.com/SanderSchaminee/fme-degreeformatter/blob/master/TestSuite.fmw) is used for testing and provides some examples.


## Usage  
### Input Coordinate Parameters   
**Attributes**  
Specify the input attribute(s) for the conversion. Note that all attributes should contain values with _the same coordinate type and input format_. If this is not the case, you will have to use multiple DegreeFormatters.

**Coordinate Type**  
An attribute can hold a latitude, longitude or latitude-longitude coordinate value. If "Latitude" is specified here, the result of the conversion will only be written to the _MyAttribute.lat_ attribute. If "Longitude" is chosen, the result will only be written to _MyAttribute.lon_ and if "Latitude and Longitude" is specified, the results will be written to the _MyAttribute.latlon_ in concatenated form and separately to the _.lat_ and _.lon_ suffixed attributes as well.

**Input Format**  
Choose between one of the following coordinate formats:  
- DD (Decimal Degrees)  
- DDM (Degrees and Decimal Minutes)  
- DMS (Degrees, Minutes and Seconds)  
- ISO 6709 (Fixed width)

Note:  
- See the parameter value selector in the transformer properties dialog for examples.  
- All decimal values should use a dot (.) as a delimiter!  
- The ISO-6709 format should officially have a trailing slash, but the DegreeFormatter allows omittance for inputs only.

### Output Coordinate Parameters 
**Output Format**  
See _Input Format_ for the supported formats. If the output format is the same as the input format, the DegreeFormatter can be used to validate and/or reformat the attribute. If validation fails, the _.lat_, _.lon_ and _.latlon_ suffixed output attributes will be empty and the failure reason will be written to _MyAttribute.error_.  
Note that if _Output Format_ is set to "ISO-6709", the output parameters below will never have an effect and the latitude-longitude output will always have a trailing slash.

**Direction Style**  
Choose if you'd like to use a N, S, E or W compass indicator after each coordinate value or not. If the _Output Format_ is "Decimal Degrees" and you choose "Use minus symbol (-) for negative coordinates", the _.lat_ and _.lon_ suffixed output attributes will contain numbers, which can be used directly in another transformer for instance. The degree symbol (°) is omitted in this case for your convenience.  
If the _Output Format_ is "Decimal Degrees" and the _Direction Style_ is set to "Use N, S, E or W", the _.lat_ and _.lon_ suffixed outputs will contain string values. For all other output formats, this is always the case.

**Decimals**  
Determines the number of decimal degrees/minutes/seconds that will be output. The default is 2.  
If this value is set to 0, all floating point values will be rounded to an integer. For instance, if the _Output Format_ is set to DMS and the value actually is 32°25′20.413″S, the output will be 32°25′20″S.  
If you don't want to round anything, simply leave this parameter empty (i.e. clear the value).

**Separation**   
Specifies how latitude-longitude coordinates and coordinate parts (in the case of DDM or DMS format) will be separated. If set to "Single comma and space" (default), coordinate parts (if any) will be concatenated and the latitude-longitude coordinate will be separated by a comma and a space.  
Note that the setting "Spaces and a comma" only affects DDM and DMS output formats.

**DM(S) Symbology**  
For DDM and DMS formats only, you can choose the symbology for the minute and second parts respectively. These symbols all look like single or double quotes. By default, the mathematical (double) prime symbol is used, but if you require a standard single/double quote for some reason, set this to "Straight Quotes". Alternatively, you can also choose "Curved Quotes".

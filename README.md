Introduction 
-------------------
This dataset contains approximately 125 million computer generated building footprints in all 50 US states. This data are freely available for download and use.  

License
-------------------
These data are licensed by Microsoft under the Open Data Commons Open Database License (ODbL) 

## FAQ
#### What the data include:
Approximately 125 million building footprint polygon geometry in all 50 US States in GeoJSON format. 
#### Creation Details:
The building extraction is done in two stages:
1.	Semantic Segmentation – Recognizing building pixels on the aerial image using DNNs
2.	Polygonization – Converting building pixel blobs into polygons
#### Semantic Segmentation
![](/images/segmentation.PNG)


DNN architecture
The network foundation is ResNet34 which can be found [here](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md#resnet). In order to produce pixel prediction output, we have appended RefineNet upsampling layers described in this [paper](https://arxiv.org/abs/1611.06612).
The model is fully-convolutional, meaning that the model can be applied on an image of any size (constrained by GPU memory, 4096x4096 in our case). 

#### Training details
The training set consists of 5 million labeled images. Majority of the satellite images cover diverse residential areas in US. For the sake of good set representation, we have enriched the set with samples from various areas covering mountains, glaciers, forests, deserts, beaches, coasts, etc.
Images in the set are of 256x256 pixel size with 1 ft/pixel resolution.
The training is done with CNTK toolkit using 32 GPUs.

#### Metrics
These are the intermediate stage metrics we use to track DNN model improvements and they are pixel based.
The pixel error on the evaluation set is 1.15%.
Pixel recall/precision = 94.5%/94.5%

#### Polygonization
![](/images/polygonization.PNG)

Method description
We developed a method that approximates the prediction pixels into polygons making decisions based on the whole prediction feature space. This is very different from standard approaches, e.g. Douglas-Pecker algorithm, which are greedy in nature. The method tries to impose some of a priory building properties, which are, at the moment, manually defined and automatically tuned. Some of these a priory properties are:
1. The building edge must be of at least some length, both relative and absolute, e.g. 3m
2. Consecutive edge angles are likely to be 90 degrees
3. Consecutive angles cannot be very sharp, smaller by some auto-tuned threshold, e.g. 30 degrees
4. Building angles likely have very few dominant angles, meaning all building edges are forming angle of (dominant angle +- n*pi/2)

In near future, we will be looking to deduce this automatically from the vast existing building information.

#### Metrics
Building matching metrics:
1. Precision = 99.3%
2. Recall = 93.5%

We track various metrics to measure the quality of the output:
1. Intersection over Union – This is the standard metric measuring the overlap quality against the labels
2. Shape distance – With this metric we measure the polygon outline similarity
3. Dominant angle rotation error – This measures the polygon rotation deviation

![](/images/bldgmetrics.JPG)

On our evaluation set contains ~15k building. The metrics on the set are:
1. IoU is 0.85, Shape distance is 0.33, Average rotation error is 1.6 degrees
2. The metrics are better or similar compared to OSM building metrics against the labels

| State         | Number of Bldgs  | Unziped MB  |
| ------------- |:-------------:| -----:|
| [Alabama](https://usbuildingdata.blob.core.windows.net/usbuildings/Alabama.zip)       |2,392,171 | 711.76 |
| [Alaska](https://usbuildingdata.blob.core.windows.net/usbuildings/Alaska.zip)      | 232,159      |   123.06 |
| [Arizona](https://usbuildingdata.blob.core.windows.net/usbuildings/Arizona.zip) | 2,492,999    |    773.50 |
| [Arkansas](https://usbuildingdata.blob.core.windows.net/usbuildings/Arkansas.zip)|1,499,025|443.99|
| [California](https://usbuildingdata.blob.core.windows.net/usbuildings/California.zip)|10,556,550|3,240|
| [Colorado](https://usbuildingdata.blob.core.windows.net/usbuildings/Colorado.zip)|2,043,866|617.68|
| [Connecticut](https://usbuildingdata.blob.core.windows.net/usbuildings/Connecticut.zip)|1,156,638|350.88|
| [Delaware](https://usbuildingdata.blob.core.windows.net/usbuildings/Delaware.zip)|331,654 |99.91|
| [DistrictOfColumbia](https://usbuildingdata.blob.core.windows.net/usbuildings/DistrictofColumbia.zip)|58,330|18.00|
| [Florida](https://usbuildingdata.blob.core.windows.net/usbuildings/Florida.zip)|6,532,545|1960|
| [Georgia](https://usbuildingdata.blob.core.windows.net/usbuildings/Georgia.zip)|3,801,461|1100|
| [Hawaii]()|252,894|75.79|
| [Idaho](https://usbuildingdata.blob.core.windows.net/usbuildings/Idaho.zip)|883,618|268.31|
| [Illinois](https://usbuildingdata.blob.core.windows.net/usbuildings/Illinois.zip)|4,783,021|1380|
| [Indiana](https://usbuildingdata.blob.core.windows.net/usbuildings/Indiana.zip)|3,224,996|961.83|
| [Iowa](https://usbuildingdata.blob.core.windows.net/usbuildings/Iowa.zip)|2,013,085|584.85|
| [Kansas](https://usbuildingdata.blob.core.windows.net/usbuildings/Kansas.zip)|1,564,845|460.28|
| [Kentucky](https://usbuildingdata.blob.core.windows.net/usbuildings/Kentucky.zip)|2,363,324| 685.64 |
| [Louisiana](https://usbuildingdata.blob.core.windows.net/usbuildings/Louisiana.zip)|2,005,341|608.33|
| [Maine](https://usbuildingdata.blob.core.windows.net/usbuildings/Maine.zip)|736,346|218.32|
| [Maryland](https://usbuildingdata.blob.core.windows.net/usbuildings/Maryland.zip)|1,590,655|467.61|
| [Massachusetts](https://usbuildingdata.blob.core.windows.net/usbuildings/Massachusetts.zip)|1,982,583|596.01|
| [Michigan](https://usbuildingdata.blob.core.windows.net/usbuildings/Michigan.zip)|4,854,138|1410| 




#### Data Vintage:
The vintage of the footprints depends on the vintage of the underlying imagery. Because Bing Imagery is a composite of mutliple sources it is difficult to know the exact dates.

#### How good is the data?:
Our metrics show that in the vast majority of cases the quality is at least as good as data hand digitized buildings in OpenStreetMap. It is not perfect, particularly in dense urban areas.

#### Why are the data being released?
Microsoft has a continued interest in supporting a thriving OpenStreetMap ecosystem. 

#### Should we import the data in to OpenStreetMap?
NO. Never blindly import data in to OSM without first checking the quality locally and discussing an import plan with the local community. Always follow the OSM import community guidelines. 











# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

# Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content
in this repository under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode),
see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the
[LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation
may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries.
The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks.
Microsoft's general trademark guidelines can be found at http://go.microsoft.com/fwlink/?LinkID=254653.

Privacy information can be found at https://privacy.microsoft.com/en-us/

Microsoft and any contributors reserve all others rights, whether under their respective copyrights, patents,
or trademarks, whether by implication, estoppel or otherwise.
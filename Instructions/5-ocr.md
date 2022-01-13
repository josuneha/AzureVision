
# Read Text in Images

Optical character recognition (OCR) is a subset of computer vision that deals with reading text in images and documents. The **Computer Vision** service provides two APIs for reading text, which you'll explore in this exercise.


## Provision a Cognitive Services resource

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account associated with your Azure subscription.
2. Select the **&#65291;Create a resource** button, search for *cognitive services*, and create a **Cognitive Services** resource with the following settings:
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: *Create new > **Lab5-OCR** > OK*
    - **Region**: *Choose any available region*
    - **Name**: *Enter a unique name*
    - **Pricing tier**: Standard S0

3. Select the required checkboxes and create the resource. **Create + Review > Create**.
4. Wait for deployment to complete, and then view the deployment details.
5. When the resource has been deployed, go to it and view its **Keys and Endpoint** page. You will need the endpoint and one of the keys from this page in the next procedure.

## Prepare to use the Computer Vision SDK

In this exercise, you'll complete a partially implemented client application that uses the Computer Vision SDK to read text.

1. In Visual Studio Code, in the **Explorer** pane, browse to the **5-ocr** folder and expand the  **Python** folder.
2. Right-click the **read-text** folder and **open in integrated terminal**. Then install the Computer Vision SDK package by running the appropriate command for your language preference:

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```

3. View the contents of the **read-text** folder, and note that it contains a file for configuration settings:
    
    - **Python**: .env

    Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your cognitive services resource. Save your changes.
4. Note that the **read-text** folder contains a code file for the client application:

    - **Python**: read-text.py

    Open the code file and at the top, under the existing namespace references, find the comment **Import namespaces**. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Computer Vision SDK:


**Python**

```Python
# import namespaces
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from msrest.authentication import CognitiveServicesCredentials
```

5. In the code file for your client application, in the **Main** function, note that the code to load the configuration settings has been provided. Then find the comment **Authenticate Computer Vision client**. Then, under this comment, add the following language-specific code to create and authenticate a Computer Vision client object:


**Python**

```Python
# Authenticate Computer Vision client
credential = CognitiveServicesCredentials(cog_key) 
cv_client = ComputerVisionClient(cog_endpoint, credential)
```
    
## Use the OCR API

The **OCR** API is an optical character recognition API that is optimized for reading small to medium amounts of printed text in *.jpg*, *.png*, *.gif*, and *.bmp* format images. It supports a wide range of languages and in addition to reading text in the image it can determine the orientation of each text region and return information about the rotation angle of the text in relation to the image

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **1**. This code calls the **GetTextOcr** function, passing the path to an image file.
2. In the **read-text/images** folder, open **Lincoln.jpg** to view the image that your code will process.
3. Back in the code file, find the **GetTextOcr** function, and under the existing code that prints a message to the console, add the following code:

**Python**

```Python
# Use OCR API to read text in image
with open(image_file, mode="rb") as image_data:
    ocr_results = cv_client.recognize_printed_text_in_stream(image_data)

# Prepare image for drawing
fig = plt.figure(figsize=(7, 7))
img = Image.open(image_file)
draw = ImageDraw.Draw(img)

# Process the text line by line
for region in ocr_results.regions:
    for line in region.lines:

        # Show the position of the line of text
        l,t,w,h = list(map(int, line.bounding_box.split(',')))
        draw.rectangle(((l,t), (l+w, t+h)), outline='magenta', width=5)

        # Read the words in the line of text
        line_text = ''
        for word in line.words:
            line_text += word.text + ' '
        print(line_text.rstrip())

# Save the image with the text locations highlighted
plt.axis('off')
plt.imshow(img)
outputfile = 'ocr_results.jpg'
fig.savefig(outputfile)
print('Results saved in', outputfile)
```

4. Examine the code you added to the **GetTextOcr** function. It detects regions of printed text from an image file, and for each region it extracts the lines of text and highlights there position on the image. It then extracts the words in each line and prints them.
5. Save your changes and return to the integrated terminal for the **read-text** folder, and enter the following command to run the program:

**Python**

```
python read-text.py
```

1. When prompted, enter **1** and observe the output, which is the text extracted from the image.
2. View the **ocr_results.jpg** file that is generated in the same folder as your code file to see the annotated lines of text in the image.

## Use the Read API

The **Read** API uses a newer text recognition model than the OCR API, and performs better for larger images that contain a lot of text. It also supports text extraction from *.pdf* files, and can recognize both printed text (in multiple languages) and handwritten text (in English).

The **Read** API uses an asynchronous operation model, in which a request to start text recognition is submitted; and the operation ID returned from the request can subsequently be used to check progress and retrieve results.

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **2**. This code calls the **GetTextRead** function, passing the path to a PDF document file.
2. In the **read-text/images** folder, right-click **Rome.pdf** and select **Reveal in File Explorer**. Then in File Explorer, open the PDF file to view it.
3. Back in the code file in Visual Studio Code, find the **GetTextRead** function, and under the existing code that prints a message to the console, add the following code:

**Python**

```Python
# Use Read API to read text in image
with open(image_file, mode="rb") as image_data:
    read_op = cv_client.read_in_stream(image_data, raw=True)

    # Get the async operation ID so we can check for the results
    operation_location = read_op.headers["Operation-Location"]
    operation_id = operation_location.split("/")[-1]

    # Wait for the asynchronous operation to complete
    while True:
        read_results = cv_client.get_read_result(operation_id)
        if read_results.status not in [OperationStatusCodes.running, OperationStatusCodes.not_started]:
            break
        time.sleep(1)

    # If the operation was successfuly, process the text line by line
    if read_results.status == OperationStatusCodes.succeeded:
        for page in read_results.analyze_result.read_results:
            for line in page.lines:
                print(line.text)
```
    
4. Examine the code you added to the **GetTextRead** function. It submits a request for a read operation, and then repeatedly checks status until the operation has completed. If it was successful, the code processes the results by iterating through each page, and then through each line.
5. Save your changes and return to the integrated terminal for the **read-text** folder, and enter the following command to run the program:

**Python**

```
python read-text.py
```

6. When prompted, enter **2** and observe the output, which is the text extracted from the document.

## Read handwritten text

In addition to printed text, the **Read** API can extract handwritten text in English..

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **3**. This code calls the **GetTextRead** function, passing the path to an image file.
2. In the **read-text/images** folder, open **Note.jpg** to view the image that your code will process.
3. In the integrated terminal for the **read-text** folder, and enter the following command to run the program:


**Python**

```
python read-text.py
```

4. When prompted, enter **3** and observe the output, which is the text extracted from the document.
   
## Save your results
Save the results in the console (paste in Notepad or take screenshot) or **.jpg** file given by the 3 options of the python app. You will send an email by the end of the labs with the selected picture and results.
## More information

For more information about using the **Computer Vision** service to read text, see the [Computer Vision documentation](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text).

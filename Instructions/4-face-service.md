

# Detect, Analyze, and Recognize Faces

The ability to detect, analyze, and recognize human faces is a core AI capability. In this exercise, you'll explore two Azure Cognitive Services that you can use to work with faces in images: the **Computer Vision** service, and the **Face** service. 


## Provision a Cognitive Services resource

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account associated with your Azure subscription.
2. Select the **&#65291;Create a resource** button, search for *cognitive services*, and create a **Cognitive Services** resource with the following settings:
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: **Create new > Lab4-Face > OK*
    - **Region**: *West Europe*
    - **Name**: *Enter a unique name*
    - **Pricing tier**: Standard S0
3. Select the required checkboxes and create the resource. **Review + Create > Create**
4. Wait for deployment to complete, and then view the deployment details.
5. When the resource has been deployed, go to it and view its **Keys and Endpoint** page. You will need the endpoint and one of the keys from this page in the next procedure.

## Prepare to use the Computer Vision SDK

In this exercise, you'll complete a partially implemented client application that uses the Computer Vision SDK to analyze faces in an image.

1. In Visual Studio Code, in the **Explorer** pane, browse to the **4-face** folder and expand the **Python** folder.
2. Right-click the **computer-vision** folder and **open in integrated terminal**. Then install the Computer Vision SDK package by running the appropriate command for your language preference:

    **Python**

    ```
    pip install azure-cognitiveservices-vision-computervision==0.7.0
    ```
    
3. View the contents of the **computer-vision** folder, and note that it contains a file for configuration settings:
    
    - **Python**: .env

4. Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your cognitive services resource. Save your changes.

5. Note that the **computer-vision** folder contains a code file for the client application:

    - **Python**: detect-faces.py

6. Open the code file and at the top, under the existing namespace references, find the comment **Import namespaces**. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Computer Vision SDK:


    **Python**

    ```Python
    # import namespaces
    from azure.cognitiveservices.vision.computervision import ComputerVisionClient
    from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
    from msrest.authentication import CognitiveServicesCredentials
    ```

## View the image you will analyze

In this exercise, you will use the Computer Vision service to analyze an image of people.

1. In Visual Studio Code, expand the **computer-vision** folder and the **images** folder it contains.
2. Select the **people.jpg** image to view it.

## Detect faces in an image

Now you're ready to use the SDK to call the Computer Vision service and detect faces in an image.

1. In the code file for your client application ( **detect-faces.py**), in the **Main** function, note that the code to load the configuration settings has been provided. Then find the comment **Authenticate Computer Vision client**. Then, under this comment, add the following language-specific code to create and authenticate a Computer Vision client object:

    **Python**

    ```Python
    # Authenticate Computer Vision client
    credential = CognitiveServicesCredentials(cog_key) 
    cv_client = ComputerVisionClient(cog_endpoint, credential)
    ```

2. In the **Main** function, under the code you just added, note that the code specifies the path to an image file and then passes the image path to a function named **AnalyzeFaces**. This function is not yet fully implemented.

3. In the **AnalyzeFaces** function, under the comment **Specify features to be retrieved (faces)**, add the following code:

    **Python**

    ```Python
    # Specify features to be retrieved (faces)
    features = [VisualFeatureTypes.faces]
    ```

4. In the **AnalyzeFaces** function, under the comment **Get image analysis**, add the following code:

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

    # Get faces
    if analysis.faces:
        print(len(analysis.faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in analysis.faces:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Person aged approximately {}'.format(face.age)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('Results saved in', outputfile)
```

5. Save your changes and return to the integrated terminal for the **computer-vision** folder, and enter the following command to run the program:

    **Python**

    ```
    python detect-faces.py
    ```

6. Observe the output, which should indicate the number of faces detected.
7. View the **detected_faces.jpg** file that is generated in the same folder as your code file to see the annotated faces. In this case, your code has used the attributes of the face to estimate the age of each person in the image, and the bounding box coordinates to draw a rectangle around each face.

## Save your results
Save the results in the console and output picture (paste in Notepad or take screenshot). You will send an email by the end of the labs with the results.

## Prepare to use the Face SDK

While the **Computer Vision** service offers basic face detection (along with many other image analysis capabilities), the **Face** service provides more comprehensive functionality for facial analysis and recognition.


1. In Visual Studio Code, in the **Explorer** pane, browse to the **4-face** folder and expand the **Python** folder.
2. Right-click the **face-api** folder and **open in integrated terminal**. Then install the Face SDK package by running the appropriate command for your language preference:

    **Python**

    ```
    pip install azure-cognitiveservices-vision-face==0.4.1
    ```
    
3. View the contents of the **face-api** folder, and note that it contains a file for configuration settings:
    
    - **Python**: .env

4. Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your cognitive services resource. Save your changes.

5. Note that the **face-api** folder contains a code file for the client application:

    - **Python**: analyze-faces.py

6. Open the code file and at the top, under the existing namespace references, find the comment **Import namespaces**. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Computer Vision SDK:

    
    **Python**

    ```Python
    # Import namespaces
    from azure.cognitiveservices.vision.face import FaceClient
    from azure.cognitiveservices.vision.face.models import FaceAttributeType
    from msrest.authentication import CognitiveServicesCredentials
    ```

7. In the **Main** function, note that the code to load the configuration settings has been provided. Then find the comment **Authenticate Face client**. Then, under this comment, add the following language-specific code to create and authenticate a **FaceClient** object:

    

    **Python**

    ```Python
    # Authenticate Face client
    credentials = CognitiveServicesCredentials(cog_key)
    face_client = FaceClient(cog_endpoint, credentials)
    ```

8. In the **Main** function, under the code you just added, note that the code displays a menu that enables you to call functions in your code to explore the capabilities of the Face service. You will implement these functions in the remainder of this exercise.

## Detect and analyze faces

One of the most fundamental capabilities of the Face service is to detect faces in an image, and determine their attributes, such as age, emotional expressions, hair color, the presence of spectacles, and so on.

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **1**. This code calls the **DetectFaces** function, passing the path to an image file.
2. Find the **DetectFaces** function in the code file, and under the comment **Specify facial features to be retrieved**, add the following code:

    **Python**

    ```Python
    # Specify facial features to be retrieved
    features = [FaceAttributeType.age,
                FaceAttributeType.emotion,
                FaceAttributeType.glasses]
    ```

3. In the **DetectFaces** function, under the code you just added, find the comment **Get faces** and add the following code:


**Python**

```Python
# Get faces
with open(image_file, mode="rb") as image_data:
    detected_faces = face_client.face.detect_with_stream(image=image_data,
                                                            return_face_attributes=features)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            print('\nFace ID: {}'.format(face.face_id))
            detected_attributes = face.face_attributes.as_dict()
            age = 'age unknown' if 'age' not in detected_attributes.keys() else int(detected_attributes['age'])
            print(' - Age: {}'.format(age))

            if 'emotion' in detected_attributes:
                print(' - Emotions:')
                for emotion_name in detected_attributes['emotion']:
                    print('   - {}: {}'.format(emotion_name, detected_attributes['emotion'][emotion_name]))
            
            if 'glasses' in detected_attributes:
                print(' - Glasses:{}'.format(detected_attributes['glasses']))

            # Draw and annotate face
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Face ID: {}'.format(face.face_id)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('\nResults saved in', outputfile)
```

4. Examine the code you added to the **DetectFaces** function. It analyzes an image file and detects any faces it contains, including attributes for age, emotions, and the presence of spectacles. The details of each face are displayed, including a unique face identifier that is assigned to each face; and the location of the faces is indicated on the image using a bounding box.
5. Save your changes and return to the integrated terminal for the **face-api** folder, and enter the following command to run the program:

   

    **Python**

    ```
    python analyze-faces.py
    ```

6. When prompted, enter **1** and observe the output, which should include the ID and attributes of each face detected.
7. View the **detected_faces.jpg** file that is generated in the same folder as your code file to see the annotated faces.

## Compare faces

A common task is to compare faces, and find faces that are similar. In this scenario, you do not need to *identify* the person in the images, just determine whether multiple images show the *same* person (or at least someone who looks similar). 

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **2**. This code calls the **CompareFaces** function, passing the path to two image files (**person1.jpg** and **people.jpg**).
2. Find the **CompareFaces** function in the code file, and under the existing code that prints a message to the console, add the following code:

**Python**

```Python
# Determine if the face in image 1 is also in image 2
with open(image_1, mode="rb") as image_data:
    # Get the first face in image 1
    image_1_faces = face_client.face.detect_with_stream(image=image_data)
    image_1_face = image_1_faces[0]

    # Highlight the face in the image
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_1)
    draw = ImageDraw.Draw(image)
    color = 'lightgreen'
    r = image_1_face.face_rectangle
    bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
    draw = ImageDraw.Draw(image)
    draw.rectangle(bounding_box, outline=color, width=5)
    plt.imshow(image)
    outputfile = 'face_to_match.jpg'
    fig.savefig(outputfile)

# Get all the faces in image 2
with open(image_2, mode="rb") as image_data:
    image_2_faces = face_client.face.detect_with_stream(image=image_data)
    image_2_face_ids = list(map(lambda face: face.face_id, image_2_faces))

    # Find faces in image 2 that are similar to the one in image 1
    similar_faces = face_client.face.find_similar(face_id=image_1_face.face_id, face_ids=image_2_face_ids)
    similar_face_ids = list(map(lambda face: face.face_id, similar_faces))

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_2)
    draw = ImageDraw.Draw(image)

    # Draw and annotate matching faces
    for face in image_2_faces:
        if face.face_id in similar_face_ids:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline='lightgreen', width=10)
            plt.annotate('Match!',(r.left, r.top + r.height + 15), backgroundcolor='white')

    # Save annotated image
    plt.imshow(image)
    outputfile = 'matched_faces.jpg'
    fig.savefig(outputfile)
```

3. Examine the code you added to the **CompareFaces** function. It finds the first face in image 1 and annotates it in a new image file named **face_to_match.jpg**. Then it finds all of the faces in image 2, and uses their face IDs to find the ones that are similar to image 1. The similar ones are annotated and saved in a new image named **matched_faces.jpg**.
4. Save your changes and return to the integrated terminal for the **face-api** folder, and enter the following command to run the program:

    **Python**

    ```
    python analyze-faces.py
    ```
    
5. When prompted, enter **2** and observe the output. Then view the **face_to_match.jpg** and  **matched_faces.jpg** files that are generated in the same folder as your code file to see the matched faces.
6. Edit the code in the **Main** function for menu option **2** to compare **person2.jpg** to **people.jpg** and then re-run the program and view the results.

## Train a facial recognition model

There may be scenarios where you need to maintain a model of specific people whose faces can be recognized by an AI application. For example, to facilitate a biometric security system that uses facial recognition to verify the identity of employees in a secure building.

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **3**. This code calls the **TrainModel** function, passing the name and ID for a new **PersonGroup** that will be registered in your cognitive services resource, and a list of employee names.
2. In the **face-api/images** folder, observe that there are folders with the same names as the employees. Each folder contains mutliple images of the named employee.
3. Find the **TrainModel** function in the code file, and under the existing code that prints a message to the console, add the following code:


**Python**

```Python
# Delete group if it already exists
groups = face_client.person_group.list()
for group in groups:
    if group.person_group_id == group_id:
        face_client.person_group.delete(group_id)

# Create the group
face_client.person_group.create(group_id, group_name)
print ('Group created!')

# Add each person to the group
print('Adding people to the group...')
for person_name in image_folders:
    # Add the person
    person = face_client.person_group_person.create(group_id, person_name)

    # Add multiple photo's of the person
    folder = os.path.join('images', person_name)
    person_pics = os.listdir(folder)
    for pic in person_pics:
        img_path = os.path.join(folder, pic)
        img_stream = open(img_path, "rb")
        face_client.person_group_person.add_face_from_stream(group_id, person.person_id, img_stream)

# Train the model
print('Training model...')
face_client.person_group.train(group_id)

# Get the list of people in the group
print('Facial recognition model trained with the following people:')
people = face_client.person_group_person.list(group_id)
for person in people:
    print('-', person.name)
```

4. Examine the code you added to the **TrainModel** function. It performs the following tasks:
    - Gets a list of **PersonGroup**s registered in the service, and deletes the specified one if it exists.
    - Creates a group with the specified ID and name.
    - Adds a person to the group for each name specified, and adds the multiple images of each person.
    - Trains a facial recognition model based on the named people in the group and their face images.
    - Retrieves a list of the named people in the group and displays them.
5. Save your changes and return to the integrated terminal for the **face-api** folder, and enter the following command to run the program:

    **Python**

    ```
    python analyze-faces.py
    ```

6. When prompted, enter **3** and observe the output, noting that the **PersonGroup** created includes two people.

## Recognize faces

Now that you have defined a **PeopleGroup** and trained a facial recognition model, you can use it to recognize named individuals in an image.

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **4**. This code calls the **RecognizeFaces** function, passing the path to an image file (**people.jpg**) and the ID of the **PeopleGroup** to be used for face identification.
2. Find the **RecognizeFaces** function in the code file, and under the existing code that prints a message to the console, add the following code:


**Python**

```Python
# Detect faces in the image
with open(image_file, mode="rb") as image_data:

    # Get faces
    detected_faces = face_client.face.detect_with_stream(image=image_data)

    # Get a list of face IDs
    face_ids = list(map(lambda face: face.face_id, detected_faces))

    # Identify the faces in the people group
    recognized_faces = face_client.face.identify(face_ids, group_id)

    # Get names for recognized faces
    face_names = {}
    if len(recognized_faces) > 0:
        print(len(recognized_faces), 'faces recognized.')
        for face in recognized_faces:
            person_name = face_client.person_group_person.get(group_id, face.candidates[0].person_id).name
            print('-', person_name)
            face_names[face.face_id] = person_name

    # Annotate faces in image
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    for face in detected_faces:
        r = face.face_rectangle
        bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
        draw = ImageDraw.Draw(image)
        if face.face_id in face_names:
            # If the face is recognized, annotate in green with the name
            draw.rectangle(bounding_box, outline='lightgreen', width=3)
            plt.annotate(face_names[face.face_id],
                        (r.left, r.top + r.height + 15), backgroundcolor='white')
        else:
            # Otherwise, just annotate the unrecognized face in magenta
            draw.rectangle(bounding_box, outline='magenta', width=3)

    # Save annotated image
    plt.imshow(image)
    outputfile = 'recognized_faces.jpg'
    fig.savefig(outputfile)

    print('\nResults saved in', outputfile)
```
    
3. Examine the code you added to the **RecognizeFaces** function. It finds the faces in the image and creates a list of their IDs. Then it uses the people group to try to identify the faces in the list of face IDs. The recognized faces are annotated wuth the name of the identified person, and the results are saved in **recognized_faces.jpg**.
4. Save your changes and return to the integrated terminal for the **face-api** folder, and enter the following command to run the program:

    **Python**

    ```
    python analyze-faces.py
    ```

5. When prompted, enter **4** and observe the output. Then view the **recognized_faces.jpg** file that is generated in the same folder as your code file to see the identified people.
6. Edit the code in the **Main** function for menu option **4** to to recognize faces in **people2.jpg** and then re-run the program and view the results. The same people should be recognized.

## Verify a face

Facial recognition is often used for identity verification. With the Face service, you can verify a face in an image by comparing it to another face, or from a person registered in a **PersonGroup**.

1. In the code file for your application, in the **Main** function, examine the code that runs if the user selects menu option **5**. This code calls the **VerifyFace** function, passing the path to an image file (**person1.jpg**), a name, and the ID of the **PeopleGroup** to be used for face identification.
2. Find the **VerifyFace** function in the code file, and under the comment **Get the ID of the person from the people group** (above the code that prints the result) add the following code:


**Python**

```Python
# Get the ID of the person from the people group
people = face_client.person_group_person.list(group_id)
for person in people:
    if person.name == person_name:
        person_id = person.person_id

        # Get the first face in the image
        with open(person_image, mode="rb") as image_data:
            faces = face_client.face.detect_with_stream(image=image_data)
            if len(faces) > 0:
                face_id = faces[0].face_id

                # We have a face and an ID. Do they match?
                verification = face_client.face.verify_face_to_person(face_id, person_id, group_id)
                if verification.is_identical:
                    result = 'Verified'
```

3. Examine the code you added to the **VerifyFace** function. It looks up the ID of the person in the group with the specified name. If the person exists, the code gets the face ID of the first face in the image. Finally, if there is a face in the image, the code verifies it against the ID of the specified person.
4. Save your changes and return to the integrated terminal for the **face-api** folder, and enter the following command to run the program:

    **Python**

    ```
    python analyze-faces.py
    ```

5. When prompted, enter **5** and observe the result.
6. Edit the code in the **Main** function for menu option **5** to to experiment with different combinations of names and the images **person1.jpg** and **person2.jpg**.
   
## Save your results

Save the **.jpg** image results given by the python app. You will send an email by the end of the labs with the selected picture and results.

## More information

For more information about using the **Computer Vision** service for face detection, see the [Computer Vision documentation](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces).

To learn more about the **Face** service, see the [Face documentation](https://docs.microsoft.com/azure/cognitive-services/face/).

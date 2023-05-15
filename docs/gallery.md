## Data structure

> Gallery Inerface

    import { Schema, model, Document } from 'mongoose';
    interface IGallery extends Document {
       key : string,
	   title: { [key: string]: string };
	   path: string; 
	   tags: string[];
    }

> Gallery Scheme

    const gallerySchema = new Schema<IGallery>({
        key: { type: String, required: true },
	    title: { type: Schema.Types.Mixed, required: true },
    	tags: [{ type: String }],
    	path: { type: String, required: true },
    });

> Model description

| name field | description |
|--|--|
| **key** | A unique value consisting of a string. The string does not contain spaces or special characters |
| **title** | multilingual image name. Contains an array in the form of key (language key) and value (title of image) |
| **tags** | multilingual tags for quick database search. |
| **path** | relative path to the image |


> Sample Data

     [
      {
      "_id:": "unique",
      "key" : "uniqueKey",
      "title": [
        {
          "ru" : "Изображение 1",
          "en" : "Image one"
        }
      ],
      "tags" : ["imageOne", "firstImage", "первое изображение", "изображение №1"],
      "path" : "/path/projectHash/filenameHash.jpg"
      },
       {
      "_id:": "unique",
      "key" : "uniqueKey",
      "title": [
        {
          "ru" : "Изображение 1",
          "en" : "Image one"
        }
      ],
      "tags" : ["imageOne", "firstImage", "первое изображение", "изображение №1"],
      "path" : "/path/projectHash/filenameHash.jpg"
      }
    ]

## Upload process

Use `multer` and `aws-sdk` to upload files. The file upload driver must be scalable to be able to connect third-party cloud services

> Description of the driver for loading the image

    import { Request } from 'express';
    import multer, { StorageEngine } from 'multer';
    import aws from 'aws-sdk';
    import multerS3 from 'multer-s3'; //native driver for multer
    
    
    interface FileUploadOptions {
      storage: StorageEngine;
    }
    
    
    abstract class FileUpload {
      protected upload: multer.Instance;
    
      constructor(options: FileUploadOptions) {
        this.upload = multer({ storage: options.storage });
      }
    
      abstract single(fieldName: string): any;
    }
    
    class S3FileUpload extends FileUpload {
      constructor(options: FileUploadOptions) {
        super(options);
      }
    
      single(fieldName: string): any {
        return this.upload.single(fieldName);
      }
    }
    
    
    class DOFileUpload extends FileUpload {
      constructor(options: FileUploadOptions) {
        super(options);
      }
    
      single(fieldName: string): any {
        return this.upload.single(fieldName);
      }
    }
    
    
    class ExampleStorage extends FileUpload {
      constructor(options: FileUploadOptions) {
        super(options);
      }
    
      single(fieldName: string): any {
        return this.upload.single(fieldName);
      }
    }
    
    
    export function createFileUploader(
      storageType: 's3' | 'do' | 'exampleStorage',
      storageOptions: any
    ): FileUpload {
      let fileUpload: FileUpload;
    
      switch (storageType) {
        case 's3':
          const s3 = new aws.S3(storageOptions);
          fileUpload = new S3FileUpload({ storage: multerS3({ s3, ...storageOptions }) });
          break;
        case 'do':
          fileUpload = new DOFileUpload({ storage: {...anyCode} }); //Driver call for DO
          break;
        case 'exampleStorage':
          fileUpload = new ExampleStorage({ storage: {...anyCode} }); //Driver call for ExampleStorage
          break;
        default:
          throw new Error('Invalid storage type');
      }
    
      return fileUpload;
    }


## Requirement Backend

1. Implement Create, Read, Delete to work with image files.

2. Implement image loading in the system, with specific data, with reference to a specific project. 
The image is uploaded to any storage.

3. Implement image acquisition.
    1. By specific key, id (ObjectId string).
    2. Image search by tags
   
4. Deletion occurs by a specific key within one project. Deleted in the repository


## Frontend requirement
Implement a React component to call a single image by its key

**Example**:

    import React, { useEffect, useState } from 'react';
    
    const GalleryImage = ({ key }) => {
      const [imageUrl, setImageUrl] = useState('');
    
      useEffect(() => {
        fetch(`/api/gallery?key=${title}`)
          .then((response) => response.json())
          .then((data) => {
            setImageUrl(data.url);
          })
          .catch((error) => {
            console.error('Failed to fetch image', error);
            setImageUrl('defaultImageError.png')
          });
      }, [title]);
    
      return (
        <div>
          {imageUrl ? (
            <img src={imageUrl} alt={title} description={description}/>
          ) : (
            <span>Loading image...</span>
          )}
        </div>
      );
    };
    
    export default GalleryImage;


Implement fallback by loading images and display a list of images with all parameters

**Example upload:**

    import React, { useState } from 'react';
    
    const ImageUpload = () => {
      const [selectedImage, setSelectedImage] = useState(null);
      const [uploading, setUploading] = useState(false);
    
      const handleImageUpload = async (event) => {
        const file = event.target.files[0];
        setSelectedImage(URL.createObjectURL(file));
        setUploading(true);
    
        try {
          const formData = new FormData();
          formData.append('image', file);
    
          const response = await fetch('/api/upload', {
            method: 'POST',
            body: formData,
          });
    
          if (response.ok) {
            // popup or notification
          } else {
                console.log('Error message)
          }
        } catch (error) {
          console.error('Failed to upload image', error);
    
        } finally {
          setUploading(false);
        }
      };
    
      return (
        <div>
          <input type="file" onChange={handleImageUpload} />
    
          {uploading && <span>Uploading...</span>}
    
          {selectedImage && (
            <div>
              <h3>Preview:</h3>
              <img src={selectedImage} alt="Preview" />
            </div>
          )}
        </div>
      );
    };
    
    export default ImageUpload;


Or use ready-made solutions by wrapping them in mappers and adapters.
For example: `https://www.getlightboxjs.com/`


## Mobile platform requirement.

The component must be isolated and connected as a library to any view.

> Example Flutter:

    import 'package:flutter/material.dart';
    import 'package:http/http.dart' as http;
    import 'dart:convert';
    
    class Image extends StatefulWidget {
      final String key;
    
      Image({required this.key});
    
      @override
      _ImageState createState() => _ImageState();
    }
    
    class _ImageState extends State<Image> {
      String imageUrl = "";
    
      @override
      void initState() {
        super.initState();
        getImage();
      }
    
      Future<void> getImage() async {
        final response =
            await http.get(Uri.parse("http://your-server-url.com/api/images/${widget.key}"));
    
        if (response.statusCode == 200) {
          final data = jsonDecode(response.body);
          setState(() {
            imageUrl = data['url'];
          });
        } else {
          throw Exception('Failed to load image');
          //or return base64 default image
        }
      }
    
      @override
      Widget build(BuildContext context) {
        return imageUrl.isNotEmpty
            ? Image.network(imageUrl)
            : Center(child: CircularProgressIndicator());
      }
    }


> Example usage in code:

    Image imageComponent = Image(key: 'arrow12');
    
    
    @override
    Widget build(BuildContext context) {
      return Scaffold(
        appBar: AppBar(
          title: Text('Example app'),
        ),
        body: Center(
          child: imageComponent,
        ),
      );
    }

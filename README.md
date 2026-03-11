# 📸 Serverless Image Processing Pipeline

This project is a **Serverless Image Processing System** built using **AWS Lambda**, **S3**, **API Gateway**, and **DynamoDB**. Users can upload images, and the system automatically processes them (e.g., resizing, optimizing), stores the results, and allows users to access them via an API.

---

## 📌 Features

* Upload images and process automatically (resize & optimize)
* Store original and processed images separately
* Store metadata in DynamoDB
* Access images through API Gateway
* Fully serverless (no servers to manage)
* CI/CD pipeline supported (Jenkins / AWS CodePipeline)
* Monitoring via CloudWatch

---

## 🛠 Services Used

| AWS Service                    | Purpose                                                    |
| ------------------------------ | ---------------------------------------------------------- |
| **S3**                         | Store original images and optimized images                 |
| **Lambda**                     | Process images (resize & optimize)                         |
| **DynamoDB**                   | Store metadata of images (like file name, size, timestamp) |
| **API Gateway**                | Provide API access to processed images                     |
| **CloudWatch**                 | Monitor Lambda executions and set alerts                   |
| **Jenkins / AWS CodePipeline** | Automate deployment (optional)                             |

---

## 🗂 Project Flow (Step by Step)

```
User Uploads Image → S3 (original) → Lambda (process) → S3 (optimized) → DynamoDB (metadata) → API Gateway → User Requests Image
```

1. **User Uploads Image:**

   * Users upload images via web interface or API.
   * The image is stored in the **original-images S3 bucket**.

2. **AWS Lambda Image Processing:**

   * Lambda is triggered whenever a new image is uploaded.
   * Lambda reads the image, **resizes and optimizes** it.
   * The processed image is stored in the **optimized-images S3 bucket**.

3. **Metadata Storage:**

   * Lambda also stores metadata (filename, timestamp, size, etc.) in **DynamoDB**.
   * This allows quick access to information about processed images.

4. **API Access:**

   * Users can request processed images via **API Gateway**.
   * API Gateway routes requests to Lambda or directly to S3 (depending on setup).

5. **Monitoring & Alerts:**

   * **CloudWatch** monitors Lambda execution, errors, and performance.
   * You can set alerts to notify you if the pipeline fails.

6. **CI/CD Pipeline (Optional):**

   * You can use **Jenkins** or **AWS CodePipeline** to automate code deployment for Lambda.
   * Any code change in GitHub can be automatically deployed to Lambda.

---

## ✅ Step-by-Step Setup Instructions

### Step 1 — Create S3 Buckets

1. Go to AWS → **S3 → Create bucket**
2. Create two buckets:

   * `original-images` → for uploaded images
   * `optimized-images` → for processed images
3. Keep both in the same region.

---

### Step 2 — Create IAM Role for Lambda

1. Go to **IAM → Roles → Create Role**
2. Trusted entity: **Lambda**
3. Attach Policies:

   * `AmazonS3FullAccess`
   * `AmazonDynamoDBFullAccess`
   * `CloudWatchLogsFullAccess`
4. Name the role: `ImageProcessingRole`

---

### Step 3 — Create Lambda Function

1. Go to **AWS Lambda → Create function → Author from scratch**
2. Name: `ImageProcessor`
3. Runtime: Python 3.10
4. Permissions: Use the **ImageProcessingRole**
5. Paste your Lambda code (see below) and update bucket names:

```python
import boto3
from PIL import Image
import io
import datetime

s3 = boto3.client('s3')
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('ImageMetadata')

SOURCE_BUCKET = 'original-images'
DEST_BUCKET = 'optimized-images'

def lambda_handler(event, context):
    # Get uploaded image
    key = event['Records'][0]['s3']['object']['key']
    file = s3.get_object(Bucket=SOURCE_BUCKET, Key=key)
    image = Image.open(file['Body'])
    
    # Resize & optimize
    image = image.resize((500,500))
    buffer = io.BytesIO()
    image.save(buffer, 'JPEG')
    buffer.seek(0)
    
    # Save processed image
    s3.put_object(Bucket=DEST_BUCKET, Key=f'processed-{key}', Body=buffer)
    
    # Save metadata
    table.put_item(Item={
        'filename': key,
        'processed_key': f'processed-{key}',
        'timestamp': str(datetime.datetime.now())
    })
    
    return {'status': 'success', 'file': key}
```

---

### Step 4 — Connect Lambda to S3 Events

1. Go to **original-images S3 bucket → Properties → Event notifications**
2. Create new event:

   * Event type: **All object create events**
   * Destination: Lambda → `ImageProcessor`

---

### Step 5 — Create DynamoDB Table

1. Go to **DynamoDB → Create table**
2. Table name: `ImageMetadata`
3. Primary key: `filename` (String)

---

### Step 6 — Create API Gateway

1. Go to **API Gateway → Create REST API**
2. Create **POST / GET method** → Lambda integration → `ImageProcessor`
3. Deploy API → note **Invoke URL**
4. Users can now request processed images via API.

---

### Step 7 — Test the Pipeline

1. Upload an image to `original-images` bucket → check `optimized-images` bucket
2. Metadata appears in DynamoDB
3. API URL serves processed images

---

### Step 8 — Monitoring

1. Go to **CloudWatch → Logs → Lambda**
2. Check logs for errors or performance metrics

---

### Step 9 — CI/CD Pipeline (Optional)

* Use **Jenkins** or **AWS CodePipeline** to automatically deploy Lambda code from GitHub.

---

## 📁 Project Folder Structure

```
serverless-image-processing/
├─ lambda_function.py       # Lambda code for processing images
├─ requirements.txt         # Python packages like Pillow
├─ README.md                # This file
├─ docs/                    # Optional diagrams/screenshots
```


## 📌 Features

* Upload images and process automatically (resize & optimize)
* Store original and processed images separately
* Store metadata in DynamoDB
* Access images through API Gateway
* Fully serverless (no servers to manage)
* CI/CD pipeline supported (Jenkins / AWS CodePipeline)
* Monitoring via CloudWatch

---

## 🛠 Services Used

| AWS Service                    | Purpose                                                    |
| ------------------------------ | ---------------------------------------------------------- |
| **S3**                         | Store original images and optimized images                 |
| **Lambda**                     | Process images (resize & optimize)                         |
| **DynamoDB**                   | Store metadata of images (like file name, size, timestamp) |
| **API Gateway**                | Provide API access to processed images                     |
| **CloudWatch**                 | Monitor Lambda executions and set alerts                   |
| **Jenkins / AWS CodePipeline** | Automate deployment (optional)                             |

---

## 🗂 Project Flow (Step by Step)

```
User Uploads Image → S3 (original) → Lambda (process) → S3 (optimized) → DynamoDB (metadata) → API Gateway → User Requests Image
```

1. **User Uploads Image:**

   * Users upload images via web interface or API.
   * The image is stored in the **original-images S3 bucket**.

2. **AWS Lambda Image Processing:**

   * Lambda is triggered whenever a new image is uploaded.
   * Lambda reads the image, **resizes and optimizes** it.
   * The processed image is stored in the **optimized-images S3 bucket**.

3. **Metadata Storage:**

   * Lambda also stores metadata (filename, timestamp, size, etc.) in **DynamoDB**.
   * This allows quick access to information about processed images.

4. **API Access:**

   * Users can request processed images via **API Gateway**.
   * API Gateway routes requests to Lambda or directly to S3 (depending on setup).

5. **Monitoring & Alerts:**

   * **CloudWatch** monitors Lambda execution, errors, and performance.
   * You can set alerts to notify you if the pipeline fails.

6. **CI/CD Pipeline (Optional):**

   * You can use **Jenkins** or **AWS CodePipeline** to automate code deployment for Lambda.
   * Any code change in GitHub can be automatically deployed to Lambda.

---

## ✅ Step-by-Step Setup Instructions

### Step 1 — Create S3 Buckets

1. Go to AWS → **S3 → Create bucket**
2. Create two buckets:

   * `original-images` → for uploaded images
   * `optimized-images` → for processed images
3. Keep both in the same region.

---

### Step 2 — Create IAM Role for Lambda

1. Go to **IAM → Roles → Create Role**
2. Trusted entity: **Lambda**
3. Attach Policies:

   * `AmazonS3FullAccess`
   * `AmazonDynamoDBFullAccess`
   * `CloudWatchLogsFullAccess`
4. Name the role: `ImageProcessingRole`

---

### Step 3 — Create Lambda Function

1. Go to **AWS Lambda → Create function → Author from scratch**
2. Name: `ImageProcessor`
3. Runtime: Python 3.10
4. Permissions: Use the **ImageProcessingRole**
5. Paste your Lambda code (see below) and update bucket names:

```python
import boto3
from PIL import Image
import io
import datetime

s3 = boto3.client('s3')
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('ImageMetadata')

SOURCE_BUCKET = 'original-images'
DEST_BUCKET = 'optimized-images'

def lambda_handler(event, context):
    # Get uploaded image
    key = event['Records'][0]['s3']['object']['key']
    file = s3.get_object(Bucket=SOURCE_BUCKET, Key=key)
    image = Image.open(file['Body'])
    
    # Resize & optimize
    image = image.resize((500,500))
    buffer = io.BytesIO()
    image.save(buffer, 'JPEG')
    buffer.seek(0)
    
    # Save processed image
    s3.put_object(Bucket=DEST_BUCKET, Key=f'processed-{key}', Body=buffer)
    
    # Save metadata
    table.put_item(Item={
        'filename': key,
        'processed_key': f'processed-{key}',
        'timestamp': str(datetime.datetime.now())
    })
    
    return {'status': 'success', 'file': key}
```

---

### Step 4 — Connect Lambda to S3 Events

1. Go to **original-images S3 bucket → Properties → Event notifications**
2. Create new event:

   * Event type: **All object create events**
   * Destination: Lambda → `ImageProcessor`

---

### Step 5 — Create DynamoDB Table

1. Go to **DynamoDB → Create table**
2. Table name: `ImageMetadata`
3. Primary key: `filename` (String)

---

### Step 6 — Create API Gateway

1. Go to **API Gateway → Create REST API**
2. Create **POST / GET method** → Lambda integration → `ImageProcessor`
3. Deploy API → note **Invoke URL**
4. Users can now request processed images via API.

---

### Step 7 — Test the Pipeline

1. Upload an image to `original-images` bucket → check `optimized-images` bucket
2. Metadata appears in DynamoDB
3. API URL serves processed images

---

### Step 8 — Monitoring

1. Go to **CloudWatch → Logs → Lambda**
2. Check logs for errors or performance metrics

---

### Step 9 — CI/CD Pipeline (Optional)

* Use **Jenkins** or **AWS CodePipeline** to automatically deploy Lambda code from GitHub.

---

## 📁 Project Folder Structure

```
serverless-image-processing/
├─ lambda_function.py       # Lambda code for processing images
├─ requirements.txt         # Python packages like Pillow
├─ README.md                # This file
├─ docs/                    # Optional diagrams/screenshots
```

---




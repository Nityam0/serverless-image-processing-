# serverless-image-processing-


## **1️⃣ Prerequisites (What You Need First)**

Before starting, make sure you have:

1. **AWS account** – [https://aws.amazon.com](https://aws.amazon.com)
2. **Git installed** (optional if using GitHub Desktop)
3. **Python 3.x installed** (for Lambda dependencies)
4. Your **GitHub project code** URL ready

> If you just want to run it, AWS web console is enough—no local setup is needed.

---

## **2️⃣ Clone the Code from GitHub**

You already have the project code in GitHub. To get it locally:

1. Open terminal (or Git Bash on Windows)
2. Type:

```bash
git clone <your-github-repo-url>
cd serverless-image-processing
```

> Replace `<your-github-repo-url>` with your GitHub repository link.

---

## **3️⃣ Create S3 Buckets (Storage for Images)**

1. Log in to AWS → go to **S3 service**
2. Click **Create bucket**

   * Name: `original-images` (for uploaded images)
   * Region: any (e.g., **us-east-1**)
   * Leave other settings default → Click **Create bucket**
3. Repeat → Create `processed-images` (for processed images)

> These buckets store the images before and after processing.

---

## **4️⃣ Create an IAM Role for Lambda**

1. Go to **AWS IAM → Roles → Create role**
2. Trusted entity → **AWS service → Lambda** → Click **Next**
3. Attach policy → search & attach:

   * `AmazonS3FullAccess`
   * `CloudWatchLogsFullAccess`
4. Name the role: `LambdaS3Role` → Click **Create Role**

> This lets Lambda read/write S3 and log errors.

---

## **5️⃣ Create Lambda Function**

1. Go to **AWS Lambda → Create function → Author from scratch**
2. Name: `ImageProcessor`
3. Runtime: **Python 3.10**
4. Permissions → **Use an existing role** → select `LambdaS3Role` → Create

---

## **6️⃣ Add Lambda Code**

Your GitHub already has the Lambda code (`lambda_function.py`).

### Option A: Copy-Paste (Simple)

1. Open `lambda_function.py` from GitHub
2. Go to **Lambda → Code tab → Edit code inline**
3. Paste all the code
4. Make sure to update bucket names:

```python
SOURCE_BUCKET = 'original-images'
DEST_BUCKET = 'processed-images'
```

### Option B: Upload ZIP (Optional)

1. Install Python dependencies locally if needed:

```bash
pip install pillow -t .
zip -r lambda_function.zip .
```

2. In Lambda → **Upload from → .zip file**

---

## **7️⃣ Connect Lambda to S3**

1. Go to **S3 → original-images bucket → Properties → Event notifications**
2. Click **Create event notification**

   * Event name: `UploadTrigger`
   * Event types: **All object create events**
   * Destination: **Lambda function** → select `ImageProcessor`
3. Click **Save**

> Now, every time you upload an image to `original-images`, Lambda will run automatically.

---

## **8️⃣ (Optional) Add API Gateway**

If you want users to **upload images via API**:

1. Go to **API Gateway → Create API → REST API**
2. Create **POST method → Integration type → Lambda function → select ImageProcessor**
3. Deploy API → note the **Invoke URL**

> You can use this URL with Postman or a web form to upload images.

---

## **9️⃣ Test Everything**

1. Go to **S3 → original-images → Upload image**
2. Wait a few seconds → check **processed-images bucket**
3. If the image appears, **it worked!**

Optional API test:

```bash
curl -X POST https://<your-api-url>/upload -F "image=@/path/to/image.jpg"
```

---

## **10️⃣ Clean Up (Stop Extra Costs)**

After testing:

* Delete Lambda function
* Delete S3 buckets
* Delete API Gateway
* Delete IAM role

> This ensures you don’t accidentally pay for AWS services.

---

## ✅ Summary (Easy Version)

1. Clone GitHub repo
2. Create two S3 buckets
3. Create IAM role for Lambda
4. Create Lambda function → paste code → set buckets
5. Connect Lambda to S3 upload events
6. Optional: create API Gateway for web uploads
7. Upload an image → processed image appears in processed bucket
8. Clean up AWS resources when done

---

If you want, I can **create a super-simple “one-page visual cheat sheet”** with **screenshots for each AWS click**, so literally **anyone can do it just by following pictures**.

Do you want me to do that next?

## Deployment Information

## Task  
Deploy a project of your own choosing to **AWS**.

---

## Services Used for the Deployment  
- **AWS S3**  
- **CloudFront CDN**

---



## Why These Deployment Tools?  

### **Amazon S3**  
Amazon S3 provides a cheap and beginner-friendly option to host static websites.  

This was ideal for my use case since my project is a **simple portfolio website** built with HTML, CSS, and JavaScript — requiring no server-side scripts.  

Key Benefits:  
- Low-cost hosting for static files.  
- Easy setup and configuration.  
- Highly available storage.  

---


### **Amazon CloudFront**  
While AWS S3 can host static websites, it comes with some limitations:  

1. **No HTTPS by default**  
   - S3 serves websites over HTTP, which is insecure.  
   - HTTPS is critical for protecting data and ensuring trust.  


2. **Public Access Policies**  
   - To make the site public, I had to use a bucket policy like this:  

   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "PublicReadGetObject",
               "Effect": "Allow",
               "Principal": "*",
               "Action": "s3:GetObject",
               "Resource": "arn:aws:s3:::single-page-portfolio-s3-and-cloudfront /*"
           }
       ]
   }


This exposes the S3 bucket and its content to anyone with the URL — not ideal for production.


3. **Latency Issues**  

Buckets are region-specific.

Clients far from the bucket’s region experience higher latency.




## How CloudFront Solves These Issues

# ✅ Improved Security
Instead of exposing the S3 bucket publicly, CloudFront can be the only service with access.
I updated the bucket policy to only allow CloudFront:

   ```json

{
    "Version": "2008-10-17",
    "Id": "PolicyForCloudFrontPrivateContent",
    "Statement": [
        {
            "Sid": "AllowCloudFrontServicePrincipal",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::single-page-portfolio-s3-and-cloudfront/*",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::170732362089:distribution/E1A8UBXTM5JLKS"
                }
            }
        }
    ]
}

```

This ensures only CloudFront can fetch files, adding a strong security layer.



# ✅ Global Performance with Caching

CloudFront caches static files at multiple edge locations worldwide.

Users are served content from the nearest location, reducing latency.

Provides a consistent and fast user experience for all clients.



# Final Thoughts

Given the benefits:

* Amazon S3 for cost-effective static hosting.

* Amazon CloudFront for HTTPS support, added security, and reduced latency.

I chose to combine both for this project.

While AWS Amplify could have been an option, it abstracts most configurations.
I wanted a hands-on experience to understand how things work behind the scenes.
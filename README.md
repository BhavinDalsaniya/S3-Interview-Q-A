# S3-Interview-Q-A

# AWS S3 Versioning and CRR (Cross Region Replication) Notes

## 1) Versioning Hands-on Scenarios

### a) Create bucket -> Enable versioning -> Suspend versioning -> Upload an object -> Delete the object
- Since versioning was enabled initially, even if suspended later, **delete creates a delete marker**.
- **You can recover the object** by removing the delete marker.

### b) Create bucket -> Enable versioning -> Upload file1 -> Suspend versioning -> Upload file2 -> Delete file2
- File2 was uploaded when versioning was suspended (unversioned object).
- **Delete is permanent**.
- **File2 cannot be recovered**.

### c) Create bucket -> Enable versioning -> Upload file1 -> Suspend versioning -> Upload file2 -> Delete file1
- File1 is versioned.
- Delete creates a delete marker.
- **You can recover file1** by removing the delete marker.

### d) Create bucket -> Upload file1 -> Enable versioning -> Upload file2 -> Delete file1
- File1 was uploaded when versioning was **not enabled** (unversioned object).
- **Delete is permanent**.
- **File1 cannot be recovered**.

### e) Create bucket -> Upload file1 -> Enable versioning -> Upload file2 -> Delete file2
- File2 is versioned.
- Delete creates a delete marker.
- **You can recover file2** by removing the delete marker.

### f) Create bucket -> Upload file1 -> Enable versioning -> Upload file2 -> Suspend versioning -> Delete file1
- File1 is versioned.
- Delete creates a delete marker.
- **You can recover file1** by removing the delete marker.

### g) Create bucket -> Upload file1 -> Enable versioning -> Upload file2 -> Suspend versioning -> Delete file2
- File2 is versioned.
- Delete creates a delete marker.
- **You can recover file2** by removing the delete marker.

### h) Create bucket -> Enable versioning -> Upload file1 -> Suspend versioning -> Upload file2 -> Enable versioning -> Upload file3 -> Suspend versioning -> Delete file1
- File1 is versioned.
- Delete creates a delete marker.
- **You can recover file1**.

### i) Create bucket -> Enable versioning -> Upload file1 -> Suspend versioning -> Upload file2 -> Enable versioning -> Upload file3 -> Suspend versioning -> Delete file2
- File2 is unversioned.
- Delete is permanent.
- **File2 cannot be recovered**.

### j) Create bucket -> Enable versioning -> Upload file1 -> Suspend versioning -> Upload file2 -> Enable versioning -> Upload file3 -> Suspend versioning -> Delete file3
- File3 is versioned.
- Delete creates a delete marker.
- **You can recover file3**.

---

## 2) Create a bucket -> Make S3 bucket public -> Upload object -> Make the object public -> Change object back to private
- Initially the object is public.
- You can change permissions anytime and make it private by modifying **ACL** or **bucket policy**.
- **Public access can be revoked.**

---

## 3) Is it necessary to delete the contents of an S3 bucket to delete the bucket?
- ✅ **Yes.**
- You must delete **all objects**, **all versions**, and **delete markers** if versioning was enabled.
- After that, you can delete the bucket.

**Steps to delete a bucket:**
- Delete all objects manually or use "Empty Bucket" option.
- Delete bucket.

---

## 4) Significance of creating an S3 bucket in a particular region
- ✅ **Lower latency**: Data is closer to the users.
- ✅ **Cost optimization**: Pricing differs across regions.
- ✅ **Compliance and Legal requirements**: (e.g., GDPR).
- ✅ **Disaster recovery planning**: Multi-region backups.

---

## 4.1) Demonstrate CRR (Cross Region Replication)
**Scenario:**
- Source Bucket: North Virginia (`us-east-1`)
- Destination Bucket: Mumbai (`ap-south-1`)

**Steps:**
1. Create source bucket (North Virginia) and destination bucket (Mumbai).
2. Enable **versioning** on both buckets.
3. Go to **Source bucket -> Management -> Replication -> Create Rule**.
4. Replicate all objects (or specific prefixes if required).
5. Assign necessary IAM role permissions.

**Result:** Objects uploaded in source bucket are **automatically copied** to the destination bucket.

---

## 5) If source bucket is private and destination bucket is private, will CRR work?
- ✅ **Yes.**
- CRR depends on IAM permissions and replication configuration, not on public/private settings.

## 6) If source bucket is public and destination bucket is private, will CRR work?
- ✅ **Yes.**
- Same as above, replication is internal and based on configured rules.

## 7) If source bucket is public and destination bucket is public, will CRR work?
- ✅ **Yes.**
- Public access settings do not impact CRR functioning.

## 8) If you change the object in the source bucket to public, will it automatically be public in the destination bucket?
- ❌ **No.**
- CRR **does not replicate ACLs** (Access Control Lists).
- The replicated object remains **private** unless manually changed.

---

# End of Notes

---

### (Optional) CLI Practice Tip
If you want to practice quickly:
```bash
aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration Status=Enabled
aws s3api put-bucket-replication --bucket <source-bucket> --replication-configuration file://replication.json
```

Where `replication.json` contains the replication rules.

---

# Happy Learning AWS! 🌐✨


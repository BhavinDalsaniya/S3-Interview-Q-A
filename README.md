# S3-Interview-Q-A

# AWS S3 Versioning and CRR (Cross Region Replication) Notes

---

## 1) Versioning Hands-on

### a)
- Create bucket → Enable versioning → Suspend versioning → Upload object → Delete object → Can you bring it back?  
✅ **No**, you cannot bring it back.  
**Reason**: When versioning is suspended, S3 treats objects like non-versioned ones. On delete, the object is permanently gone unless a version ID exists (but here, none was created under suspended versioning).

### b)
- Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Delete file2 → Can you bring file2 back?  
✅ **No**, you cannot bring file2 back.  
**Reason**: After suspending versioning, newly uploaded file2 has no version ID. Deleting it deletes it completely.

### c)
- Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Delete file1 → Can you bring file1 back?  
✅ **Yes**, you can bring file1 back.  
**Reason**: File1 was uploaded when versioning was enabled. When deleted, it creates a "delete marker." Removing the marker restores file1.

### d)
- Create bucket → Upload file1 → Enable versioning → Upload file2 → Delete file1 → Can you bring file1 back?  
❌ **No**, file1 cannot be brought back.  
**Reason**: File1 was uploaded before enabling versioning (non-versioned), so deletion removes it permanently.

### e)
- Create bucket → Upload file1 → Enable versioning → Upload file2 → Delete file2 → Can you bring file2 back?  
✅ **Yes**, you can bring file2 back.  
**Reason**: File2 was uploaded after versioning was enabled. Deletion creates a delete marker; removing the marker restores file2.

### f)
- Create bucket → Upload file1 → Enable versioning → Upload file2 → Suspend versioning → Delete file1 → Can you bring file1 back?  
❌ **No**, you cannot bring file1 back.  
**Reason**: File1 was uploaded when versioning was suspended (non-versioned), so deleting it wipes it permanently.

### g)
- Create bucket → Upload file1 → Enable versioning → Upload file2 → Suspend versioning → Delete file2 → Can you bring file2 back?  
✅ **Yes**, you can bring file2 back.  
**Reason**: File2 was uploaded during active versioning. Even after suspension, its version remains and can be restored.

### h)
- Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Enable versioning → Upload file3 → Suspend versioning → Delete file1 → Can you bring file1 back?  
✅ **Yes**, you can bring file1 back.  
**Reason**: File1 was uploaded when versioning was enabled. You can delete the delete marker and restore file1.

### i)
- Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Enable versioning → Upload file3 → Suspend versioning → Delete file2 → Can you bring file2 back?  
❌ **No**, you cannot bring file2 back.  
**Reason**: File2 was uploaded when versioning was suspended; deleting it is permanent.

### j)
- Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Enable versioning → Upload file3 → Suspend versioning → Delete file3 → Can you bring file3 back?  
✅ **Yes**, you can bring file3 back.  
**Reason**: File3 was uploaded during active versioning. Its version is stored and can be restored even after suspension.

---

## 2) S3 Bucket Public/Private Behavior

- Create a bucket → Make S3 bucket public → Upload an object → Make the object public → Now change the object back to private  
✅ **Yes**, you can change the object back to private.  
You can:
- Remove public ACL manually.
- Add a bucket policy/object ACL denying public access.

---

## 3) Deleting an S3 Bucket

✅ **Yes**, you must delete the bucket contents first.
- Delete all objects.
- Delete all versions and delete markers (if versioning is enabled).
- Then delete the bucket.

---

## 4) Importance of S3 Bucket Region Selection

✅ Advantages:
- **Lower latency**: Choose nearest region (e.g., Mumbai for India users).
- **Cost optimization**: Some regions are cheaper.
- **Compliance**: Meet legal requirements (e.g., GDPR).
- **Disaster recovery**: Backup across different regions.

---

### 4.1) Cross Region Replication (CRR) Demonstration — North Virginia ➡️ Mumbai

Steps:
- Create Source bucket (us-east-1).
- Create Destination bucket (ap-south-1).
- Enable versioning on both buckets.
- Setup Replication Rule: Replicate all objects → Destination bucket → Create IAM role for replication → Save rule.

✅ Objects uploaded in North Virginia will auto-replicate to Mumbai!

---

## 5) If you make both source and destination buckets private, will CRR still work?
✅ **Yes**, CRR will still work.  
It depends on IAM permissions, not public/private ACL.

---

## 6) If source is public and destination is private, will CRR work?
✅ **Yes**, CRR still works.

---

## 7) If both source and destination are public, will CRR work?
✅ **Yes**, CRR still works.  
(Though public buckets are discouraged.)

---

## 8) If you make an object public in the source, does it become public in destination?
❌ **No**.  
ACLs (permissions) are **not** copied during replication.

---

## 9) If you change the object in the source to private, what happens in destination?
- **Nothing happens.**
- Permission changes are not synced via CRR.

---

## 10) If you make the replicated object public in the destination, does it affect CRR?
✅ **No impact** on CRR.
- Replicated objects are independent once copied.

---

## 11) What happens when you delete an object in the source bucket?
- **Normally, it stays in the destination**.
- **Exception**: If delete marker replication is enabled, deletion is replicated.

---

## 12) What happens when you delete an object in the destination bucket?
- **No effect** on the source bucket.

---

## 13) What happens if you suspend versioning in the destination bucket?
❌ **CRR will stop working** for new objects.
- Versioning must remain enabled.

---

## 14) What happens if you suspend versioning in the source bucket?
❌ **CRR will stop working** for new uploads.
- Source versioning must stay enabled.

---

## 15) Create a Bucket → Create a Folder → Upload an Object

Steps:
1. Create a bucket (example: `my-bucket`).
2. Create a folder inside it (example: `my-folder/`).
3. Upload a file into the folder.
✅ File will have a key like: `my-folder/myfile.jpg`.

---

## 16) Enable versioning by default when creating an S3 bucket

- While creating a bucket → Go to "Bucket Versioning" section → Enable versioning.
✅ Versioning will be ON by default.

---

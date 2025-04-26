# S3-Interview-Q-A

# AWS S3 Versioning and CRR (Cross Region Replication) Notes

1) Versioning Hands-on
a)

Create bucket → Enable versioning → Suspend versioning → Upload object → Delete object → Can you bring it back?
✅ No, you cannot bring it back.

Reason: When versioning is suspended, S3 treats objects like non-versioned ones. On delete, the object is permanently gone unless a version ID exists (but here, none was created under suspended versioning).

b)

Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Delete file2 → Can you bring file2 back?
✅ No, you cannot bring file2 back.

Reason: After suspending versioning, newly uploaded file2 has no version ID. Deleting it deletes it completely.

c)

Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Delete file1 → Can you bring file1 back?
✅ Yes, you can bring file1 back.

Reason: file1 was uploaded when versioning was enabled. It has a version ID. When you delete, it creates a "delete marker," but you can delete the delete marker and restore file1.

d)

Create bucket → Upload file1 → Enable versioning → Upload file2 → Delete file1 → Can you bring file1 back?
❌ No, file1 cannot be brought back.

Reason: file1 was uploaded before enabling versioning (non-versioned), so deletion removes it permanently.

e)

Create bucket → Upload file1 → Enable versioning → Upload file2 → Delete file2 → Can you bring file2 back?
✅ Yes, you can bring file2 back.

Reason: file2 was uploaded after versioning was enabled, so it has a version ID. Deletion adds a delete marker — removing it restores file2.

f)

Create bucket → Upload file1 → Enable versioning → Upload file2 → Suspend versioning → Delete file1 → Can you bring file1 back?
❌ No, you cannot bring file1 back.

Reason: file1 was uploaded when versioning was suspended (non-versioned), so deleting it wipes it out permanently.

g)

Create bucket → Upload file1 → Enable versioning → Upload file2 → Suspend versioning → Delete file2 → Can you bring file2 back?
✅ Yes, you can bring file2 back.

Reason: file2 was uploaded during active versioning. Even after suspension, old versions remain. Deleting places a delete marker; you can delete the marker to bring back file2.

h)

Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Enable versioning → Upload file3 → Suspend versioning → Delete file1 → Can you bring file1 back?
✅ Yes, you can bring file1 back.

Reason: file1 was uploaded when versioning was enabled. Delete marker will be created on delete. You can remove the marker and restore file1.

i)

Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Enable versioning → Upload file3 → Suspend versioning → Delete file2 → Can you bring file2 back?
❌ No, you cannot bring file2 back.

Reason: file2 was uploaded when versioning was suspended, so it was a non-versioned object. Deletion is permanent.

j)

Create bucket → Enable versioning → Upload file1 → Suspend versioning → Upload file2 → Enable versioning → Upload file3 → Suspend versioning → Delete file3 → Can you bring file3 back?
✅ Yes, you can bring file3 back.

Reason: file3 was uploaded when versioning was active. Even after suspension, the version is saved. You can recover file3 by deleting the delete marker.
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


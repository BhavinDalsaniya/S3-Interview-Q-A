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

## 2) S3 Bucket Public/Private
Create a bucket → Make S3 bucket public → Upload an object → Make the object public → Now change the object back to private
✅ Yes, you can change the object back to private.

You either:

Remove public ACL from the object manually

OR add a bucket policy/object ACL denying public access

After doing this, the object becomes private again.

## 3) Is it necessary to delete the contents of an S3 bucket to delete the S3 bucket? How can you delete a bucket?
✅ Yes, it is necessary.

S3 does not allow deleting a bucket unless it is empty.

Steps to delete a bucket:

Delete all objects (and all object versions if versioning was enabled).

Then, delete the bucket.

If versioning is enabled, you must also delete all versions + delete markers.

## 4) What is the significance of creating an S3 bucket in a particular region?
✅ Lower latency: If your users are in India, use Mumbai region for faster access.

✅ Cost optimization: Some regions are cheaper than others.

✅ Compliance: Some data must stay in specific regions due to legal rules (example: Europe GDPR).

✅ Disaster Recovery: You may want buckets in different regions for backup.

4.1) Demonstrate CRR (Cross Region Replication) — North Virginia ➡️ Mumbai
Simple CRR Steps:

Create Source bucket in North Virginia (us-east-1).

Create Destination bucket in Mumbai (ap-south-1).

Enable versioning on both buckets (important!).

Go to Source Bucket → Management → Replication → Add rule:

Choose: Replicate all objects (or a prefix if needed).

Destination: Choose the Mumbai bucket.

IAM Role: Allow S3 to replicate.

Save rule.

✅ Now, whenever you upload an object into the North Virginia bucket, it automatically gets copied into the Mumbai bucket.

## 5) If you change your source bucket to private and destination bucket to private, will CRR still work?
✅ Yes, CRR will still work.

CRR works based on IAM permissions and replication rules.

Public/Private settings (ACL) don't affect replication working.

Only bucket permissions for replication IAM role matter.

## 6) If you change your source bucket to public and destination bucket to private, will CRR still work?
✅ Yes, CRR will still work.

Again, replication is internal AWS service-to-service, not dependent on public/private settings.

Only replication rule permissions and versioning are important.

## 7) If you change your source bucket to public and destination bucket to public, will CRR still work?
✅ Yes, CRR will still work.

Public access or private access doesn't impact CRR replication.

However, public buckets are not recommended unless absolutely needed (due to security risks).

## 8) If you change the object in the source bucket to public, will it automatically be public in the destination bucket too?
❌ No, it will not automatically become public.

When CRR replicates an object:

It copies the data.

It does not copy the ACLs (like public/private settings).

So, the object in destination bucket remains private unless you manually change it.

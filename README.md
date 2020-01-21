# Image Loader

> **Image Loader** is a Java Web application developed using App Engine Standard 
> Environment and Cloud Storage.

## Functionality

### Upload :new:
User can create new images in Google Cloud Storage bucket:
```java
    public String create(FileItemStream item) throws DaoException {
        BlobInfo blobInfo = null;
        try {
            blobInfo = storage.create(
                    BlobInfo.newBuilder(BUCKET_NAME, item.getName())
                            .setAcl(new ArrayList<>(Arrays.asList(Acl.of(User.ofAllUsers(), Role.READER)))).build(),
                    item.openStream());
        } catch (IOException e) {
            logger.error(e.getMessage());
            throw new DaoException(e.getMessage());
        }
        return blobInfo.getMediaLink();
    }
```

### Delete :x:
You can delete images from bucket:
```java
    public void delete(BlobId blobId) {
        storage.delete(blobId);
    }
```

### View :eyes:
User can list all the images stored in a bucket:
```java
    public List<Image> list() {
        Page<Blob> blobs = storage.list(BUCKET_NAME, BlobListOption.currentDirectory());
        Iterator<Blob> blobIterator = blobs.iterateAll().iterator();
        List<Image> images = new ArrayList<>();
        while (blobIterator.hasNext()) {
            Blob blob = blobIterator.next();
            if (!blob.isDirectory()) {
                Image image = new Image(new DateTime(blob.getCreateTime()).toStringRfc3339(), blob.getSize(),
                        blob.getMediaLink(), blob.getBlobId());
                images.add(image);
            }
        }
        return images;
    }
```


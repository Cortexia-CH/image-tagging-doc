# Endpoints

Basically, we have three endpoints that are interesting for us while using the VoTT web application. Two of them concern the images and one the actions.

## images

One endpoint is for getting a list of 100 images for the current user and the other is to load the image along with the previous tags.

### Renew images

This endpoint named 'renew_images' is used for getting randomly 100 images for the current user. Basically, it set the tagger id on the image table from the DB. That means that no other user can get those images while the current user is tagging them. It's also used for the previews that are seen on the left of the application. This endpoint is called every time the user logs in or ask for renewing the images with the button. When the images are renewed the user get 100 new random images. He doesn't keep the one he hasn't tagged before because before setting the current tagger id in the DB we remove it from all images.

### Loading images

This endpoint named 'with_last_action' is used to load the images along with their previous tags. That means if an image has already been tagged from another user, but isn't validated the next user will already seen its tags. This endpoint is called every time the user enters a new image.

## Actions

It's one endpoint to create sent actions from the VoTT in the DB. But each action has some different behaviour. They will be used for knowing the time spent on the app by a user on the app, on each image or to know how many images he tagged in which time. It also will be used to check if the tags are correct or not. The goal is to be able to rate the users. An action is specified as the below json but not all fields are required.

```json
{
  "timestamp": "2020-02-02T07:43:32.792Z",
  "regions": [
    {
      "id": "string",
      "tags": [
        "string"
      ],
      "points": [
        {
          "x": 0,
          "y": 0
        }
      ],
      "type": "string",
      "boundingBox": {
        "left": 0,
        "width": 0,
        "top": 0,
        "height": 0
      }
    }
  ],
  "is_modified": true,
  "user_id": 0,
  "image_id": 0
}
```

### Login

Just create à login action in the DB. The important information for this action is the timestamp and the user id. Just needed to know when a user has logged in.

### Logout

Create à logout action in the DB. The important information for this action is the timestamp and the user id. Beside creating the action, it also resets all current tagger id on the images table from the DB. That means they can be used by another user.

### Image in / image out

Create this action when the user arrives / quits an image. The important informations are the timestamp, the current user, the image id, the regions (tags) and if the tags have been modified.

### Delete

Create a delete action in the DB. After three delete actions with the same image id a flag will be set to true for the corresponding image in the DB. It never removes the corresponding image from the file system. It only means that no fourth user will get it. We don't remove it from the file system because it's interesting to know why the users have been deleting it.
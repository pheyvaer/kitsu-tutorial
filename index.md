# You and your Kitsu Anime library

This tutorial guides you through to the core elements of how you can access your [Kitsu](https://kitsu.io) anime library.
We will have a look at the different elements of the [Kitsu API](http://docs.kitsu.apiary.io) and how they can provide you with the data needed to build your application.

## Goal

The goal is to build an application that shows the library entries of a specific user, together with the information about the anime for each entry. Additionally, we provide links to the anime on the Kitsu website.

## Focus

We focus on three elements
- [users](http://docs.kitsu.apiary.io/#reference/users),
- [libraries](http://docs.kitsu.apiary.io/#reference/user-libraries), and
- [anime series](http://docs.kitsu.apiary.io/#reference/media/anime).

We are using the latest API available [https://kitsu.io/api/edge/](https://kitsu.io/api/edge/) at the time of writing (30/06/2017).
Note that the explanations will be about getting the required data from the API and not about how to, e.g., visualize the data.

## Steps

These are the different steps of this tutorial.

1. Get the `id` of a user based on his/her `username`.
2. Get the library entries of a user.
3. Interpret the information of each library entry.
4. Get the information about the anime for each entry.
5. Get the Kitsu website link for each anime.

### 1. Get user's `id`

The `id` of a user can be retreived based on the user's username (referred to as `name`).
The following API GET call gives the details about a user with username `<usersname>`:
`https://kitsu.io/api/edge/users?filter[name]=<username>`

What happens here exactly? Well, `https://kitsu.io/api/edge/users` refers to all users.
With `?filter[name]=<username>`, you apply a filter on all users to get only the user with username `<username>`.
Note that you can filter on every element of a `user` object.

Let's take my username `piehey` as an example.
That would give `https://kitsu.io/api/edge/users?filter[name]=piehey`.
The JSON response of the call looks like this in my case:

```
{
  "data": [
    {
      "attributes": {...}, 
      "id": "142706", 
      "links": {...}, 
      "relationships": {...}, 
      "type": "users"
    }
  ], 
  "links": {...}, 
  "meta": {
    "count": 1
  }
}
```
The `id` of the user can be retreived from the response via `data[0].id`, which is in our example `142706`.

### 2. Get library entries of user

The different library entries of a specific user can be retreived via the user's `id`. The GET call looks like this: `https://kitsu.io/api/edge/users/<id>/library-entries`, where `<id>` is the `id` of the user.

In our example the call looks like `https://kitsu.io/api/edge/users/142706/library-entries`.
The response is
```
{
  "data": [
    {
      "attributes": {...}, 
      "id": "17602358", 
      "links": {...}, 
      "relationships": {
        "anime": {...}, 
        "drama": {...}, 
        "manga": {...}, 
        "media": {...}, 
        "mediaReaction": {...}, 
        "nextUnit": {...}, 
        "review": {...}, 
        "unit": {...}, 
        "user": {...}
      }, 
      "type": "libraryEntries"
    }, 
    ...
  ], 
  "links": {
    "first": "https://kitsu.io/api/edge/users/142706/library-entries?page%5Blimit%5D=10&page%5Boffset%5D=0", 
    "last": "https://kitsu.io/api/edge/users/142706/library-entries?page%5Blimit%5D=10&page%5Boffset%5D=30", 
    "next": "https://kitsu.io/api/edge/users/142706/library-entries?page%5Blimit%5D=10&page%5Boffset%5D=10"
  }, 
  "meta": {
    "count": 40
  }
}

```
Note that the library entries of a specific user can also be retrieved via `https://kitsu.io/api/edge/library-entries?filter[<id>]` with `<id>` the id of the user.
The object `$.data` of the response contains all the entries.
At the bottom you see that there are 40 entries in total via `$.meta`.
However, only 10 entries are given in this reponse, as the API uses [pagination](http://docs.kitsu.apiary.io/#introduction/json-api/pagination).
The differenct pages are available via `$.links`.
The next 10 entries are retreived by doing a call to the URL in `$.next`.
The URL has an `offset` of 10, which means that the next page starts with entry 10.
You can customize the number of entries per page via the `limit` property as explained in the [documentation](http://docs.kitsu.apiary.io/#introduction/json-api/pagination).

### 3. Interpret the information of each library entry.
Now, let's have a look at a single library entry.
You can find an example below.
```
{
  "id": "17641027",
  "type": "libraryEntries",
  "links": {
    "self": "https://kitsu.io/api/edge/library-entries/17641027"
  },
  "attributes": {
    "createdAt": "2017-07-06T02:41:47.483Z",
    "updatedAt": "2017-07-06T02:41:50.879Z",
    "status": "current",
    "progress": 1,
    "volumesOwned": 0,
    "reconsuming": false,
    "reconsumeCount": 0,
    "notes": null,
    "private": false,
    "progressedAt": "2017-07-06T02:41:50.879Z",
    "startedAt": "2017-07-06T02:41:47.483Z",
    "finishedAt": null,
    "rating": "0.0",
    "ratingTwenty": null
  },
  "relationships": {...}
}
```

The most interesting information for each entry will probably be in the `attributes` object.
It contains, for example, when an entry was added to the library, when it was finished, and what its rating is.
Note that currently two ratings are available: `rating` and `ratingTwenty`.
The former is deprecated and is being replaced by `ratingTwenty`.
`rating` applies a 0.5/5 system and `ratingTwenty` applies a 2/20 system.

### 4. Get the information about the anime for each entry.
If you want to get details about the anime for each entry, you will need to look at the `relationships` objects.
A part of this object of our example looks like this.
```
"relationships": {
  "user": {
    "links": {
      "self": "https://kitsu.io/api/edge/library-entries/17641027/relationships/user",
      "related": "https://kitsu.io/api/edge/library-entries/17641027/user"
     }
  },
  "anime": {
     "links": {
        "self": "https://kitsu.io/api/edge/library-entries/17641027/relationships/anime",
        "related": "https://kitsu.io/api/edge/library-entries/17641027/anime"
     }
  },
  ...
```
`user` refers to the user to which this library entry belongs.
`anime` refers to the anime that is part of this entry.
Other relationships are also present, but we wont discuss them here.
More specific, we are focussing on anime here; however, other media, such as manga can also be part of a library entry.

The `anime` object has a number of links.
The `self` link refers to the relationships itself, while the `related` link points to the actual anime.

When we do a GET call to this link, we get the following result.
```
{
    "data": {
        "id": "12757",
        "type": "anime",
        "links": {
            "self": "https://kitsu.io/api/edge/anime/12757"
        },
        "attributes": {
            "createdAt": "2016-11-20T07:06:44.960Z",
            "updatedAt": "2017-07-08T11:21:51.581Z",
            "slug": "netsuzou-trap",
            "synopsis": "Yuma and Hotaru have been friends since childhood, so it's only natural that when Yuma is nervous about her new boyfriend, she asks Hotaru for advice. But when Hotaru starts coming onto Yuma for what feels like more than just 'practice', what does it mean...? With boyfriends in the foreground but a secret, passionate tryst in the background, will Yuma and Hotaru try to forget what happened between them or have they fallen into a trap of true love and betrayal?\r\n\r\n(Source: Seven Seas)",
            "coverImageTopOffset": 0,
            "titles": {
                "en": "NTR: Netsuzou Trap",
                "en_jp": "Netsuzou TRap",
                "ja_jp": "捏造トラップ―NTR―"
            },
            "canonicalTitle": "Netsuzou TRap",
            "abbreviatedTitles": null,
            "averageRating": null,
            "ratingFrequencies": {...},
            "userCount": 646,
            "favoritesCount": 0,
            "startDate": "2017-07-05",
            "endDate": null,
            "popularityRank": 3460,
            "ratingRank": null,
            "ageRating": null,
            "ageRatingGuide": "",
            "subtype": "TV",
            "status": "current",
            "posterImage": {...},
            "coverImage": null,
            "episodeCount": 12,
            "episodeLength": 10,
            "youtubeVideoId": "3UUs23olVOI",
            "showType": "TV",
            "nsfw": false
        },
        "relationships": {...}

}
```

As you can see a lot of information is available, but for now we only want to retreive the title of the anime.
The titles of the anime are available in `data.attributes.titles`.
```
"titles": {
    "en": "NTR: Netsuzou Trap",
    "en_jp": "Netsuzou TRap",
    "ja_jp": "捏造トラップ―NTR―"
}
```
Three titles are available for this specific anime.
You can choose between the English one 'NTR: Netsuzou Trap', the Japanese one '捏造トラップ―NTR―', or the English/Japanese one 'Netsuzou TRap'.

### 5. Get the Kitsu website link for each Anime.

Finally, we also want to have the Kitsu website link of that anime.
Every Kitsu anime starts with `https://kitsu.io/anime/`.
The slug of an anime has to be added to get the full link.
The slug can be found via `data.attributes.slug`.
For our example, the slug is 'netsuzou-trap', which results in the link `https://kitsu.io/anime/netsuzou-trap`.

## Conclusion

In this tutorial we briefly touched upon a few of the different aspects of the Kitsu API.
More options are available, so make sure to have a look at the documentation.
Furthermore, if you have questions or remarks regarding Kitsu or the Kistu API, be sure to checkout Kitsu's [GitHub](https://github.com/hummingbird-me).

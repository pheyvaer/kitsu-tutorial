# You and your Kitsu Anime library

This tutorial guides you through to the core elements of how you can access your [Kitsu](https://kitsu.io) Anime library.
We will have a look at the different elements of the [Kitsu API](http://docs.kitsu.apiary.io) and how they can provide you with the data needed to build your application.

## Goal

The goal is to build an application that shows the library entries of a specific user, together with the information about the Anime for each entry. Additionally, we provide links to the Anime on the Kitsu website.

## Focus

We focus on three elements
- [users](http://docs.kitsu.apiary.io/#reference/users),
- [libraries](http://docs.kitsu.apiary.io/#reference/user-libraries), and
- [Anime series](http://docs.kitsu.apiary.io/#reference/media/anime).

We are using the latest API available [https://kitsu.io/api/edge/](https://kitsu.io/api/edge/) at the time of writing (30/06/2017).
Note that the explanations will be about getting the required data from the API and not about how to, e.g., visualize the data.

## Steps

These are the different steps of this tutorial.

1. Get the `id` of a user based on his/her `username`.
2. Get the library entries of a user.
3. Interpret the information of each library entry.
4. Get the information about the Anime for each entry.
5. Get the Kitsu website link for each Anime.

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

```JSON
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
The object `$.data` contains all the entries.
At the bottom you see that there are 40 entries in total via `$.meta`.
However, only 10 entries are given in this reponse, as the API uses [pagination](http://docs.kitsu.apiary.io/#introduction/json-api/pagination).
The differenct pages are available via `$.links`.
The next 10 entries are retreived by doing a call to the URL in `$.next`.
The URL has an `offset` of 10, which means that the next page starts with entry 10.
You can customize the number of entries per page via the `limit` property as explained in the [documentation](http://docs.kitsu.apiary.io/#introduction/json-api/pagination).

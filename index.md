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
Note that, the explanations will be about getting the required data from the API and not about how to, e.g., visualize the data.

## Steps

This are the different steps of this tutorial.

1. Get the `id` of a user based on his/her `username`.
2. Get the library entries of a user.
3. Interpret the information of each library entry.
4. Get the information about the Anime for each entry.
5. Get the Kitsu website link for each Anime.

## Guidance
Answer the following questions considering the [learning outcomes for this week](https://learn.foundersandcoders.com/course/syllabus/developer/authentication/learning-outcomes/).
Make sure to record evidence of your processes. You can use code snippets, screenshots or any other material to support your answers.

Do not fill in the feedback section. The Founders and Coders team will update this with feedback on your progress.

## Assessment
 ### 1. Show evidence of a learning outcome you have achieved this week.
> **Ensure only authenticated users can see certain content**

The GET request for `/` was set up in `routes/home.js` such that if the user does not have a current session, they are served one set of HTML (the landing page), and if they *are* logged in, different content is served depending on whether they are registered as a "wizard" or a "demon" (the two user categories available on sign up).

```js
router.get('/', (req, res) => {
    try {
        const session = req.session
        if (!session) {
            const homePage = home.home();
            res.send(homePage);
        } else {
            const user = getUserById(session.user_id)
            const homePage = home.users(user.isWizard);
            res.send(homePage);
        }

    } catch (error) {
        console.error('Error with route:', error.message);
        throw error;
    }
});
```

The `home.home()` and `home.users()` functions are defined in `templaces/home.js`. `home.home()` simply serves up the static HTML content of the landing page. `home.users()` however is more complicated and dynamically generates different content for wizards vs. demons:

```js
function users(isWizard) {
    console.log(!isWizard)
    const title = 'PactPortal';
    const users = getUserList(isWizard ? 0 : 1);
    const content = /*html*/ `
    <div class="banner">
        <div class="title">
            <img src="images/logo.svg" /><h1>PactPortal</h1>
        </div>
        <p class="subtitle">Bringing wizards and demons together</p>
    </div>
    <div class="match-container">
        <div>
            <a href="/user"><button class="button">Profile</button></a>
            <a href="/log-out"><button class="button">Log Out</button></a>
        </div>
        <h2>${isWizard ? 'Demons' : 'Wizards'} Near You</h2>
        ${users.map(user => createPost(user)).join('')}
    </div>
`
    return layout({ title, content });
}
```

Whichever type the user is (wizard/demon), the database is queried for all the users of the opposing type. Each user's data object from the query is passed to `createPost()` via the map method, which returns html:

```js
function createPost(user) {
    return /*html*/ `
    <div class="post">
        <p class="post-username">${user.username}</p>
        <img src="${user.imageURL}" class="post-picture" />
        <p class="post-bio">${user.bio}</p>
        <button class="button">Banish</button>
    </div>
`
}
```

 ### 2. Show an example of a learning outcome you have struggled with and/or would like to re-visit.
> [**Integrate security scanning or checks into the CI/CD pipeline**]  
> I wasn't really doing DevOps this week, so I'd like to revisit this as I'm not confident on it yet.

## Feedback
### Alphonso's Feedback
#### What Went Well
Love the example on showing different content depending on a session and particularly that you delved a bit deeper into *how* the content varies for a user that is logged in.
I think a bit of an insight into this line would be a good idea too - ```const users = getUserList(isWizard ? 0 : 1);```
When I did a review of your codebase this was a nice stand out and it shows what I think is a nice and clean approach to personalising the data on screen with minimal querying.

#### Even Better If
The CI/CD pipeline can quickly become a massive rabbit whole to look into at the moment but one thing you coud easily start exploring is easy automation tools like husky, which I remember was discussed among the cohort before but was not in the last project repos.
It's a simple way to guarantee linting and testing, for example, happen at specific points (like, before someone pushes new code to origin) and it can get you thinking more broadly about the pipeline without getting too complex for the size of the projects you're currently working on.
There's a nifty guide [here](https://www.npmjs.com/package/husky)

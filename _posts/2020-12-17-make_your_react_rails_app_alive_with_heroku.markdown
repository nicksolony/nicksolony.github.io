---
layout: post
title:      "Make your React/Rails app alive with Heroku"
date:       2020-12-17 23:59:36 +0000
permalink:  make_your_react_rails_app_alive_with_heroku
---


It has been a long year and I've learned a lot in Flatiron Bootcamp. My last project was an App with React frontend and Rails Backend. Once I passed my final assessment I only had one question for my instructor "How do I make it alive?". He suggested using Heroku, so I figured it would be an interesting topic to cover in my blog.

There are 3 prerequisites:
1. You should use **PostgreSQL** database in your Rails backend.
2. **Frontend** and **backend** of the app should have it's own git **repositories**.
3. Use **Yarn** as your Node.js package manager.

Now you need to update the code and prepare it for deployment. 
Let's start with the backend. You need to update cors.rb file. In our development version, we allowed requests from our local app.

```
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'http://localhost:3000/'

    resource '*',
      headers: :any, methods: [:get, :post, :put, :patch, :delete]
  end
end
```

Now we will need to point to our new live app. Our live app name will be "**fixer-tube**", so Heroku URL will be 'https://fixer-tube.herokuapp.com'. So let's change our code as follows.

```
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'https://fixer-tube.herokuapp.com'

    resource '*',
      headers: :any, methods: [:get, :post, :put, :patch, :delete]
  end
end
```

In the frontend, we will also need some updates. In your frontend root directory, let's create static.json and add the following code
```
{ "root": "build/", "routes": { "/**": "index.html" } }
```
Since our backend will now be hosted online, we will need to update our fetch requests. Our development code to load data looked as follows:
```
export const loadData = () => {
    return (dispatch) => {
        fetch('http://localhost:3001/main_categories')
        .then(resp => resp.json())
        .then(data => dispatch({type: LOAD_DATA, payload: data}))
    }
};
```
Now, we will need a name for our backend API, lets call it **fixer-tube-API**, therefore my APIs URL once on Heroku will be - 'https://fixer-tube-api.herokuapp.com/main_categories'.

```
export const loadData = () => {
    return (dispatch) => {
        fetch('https://fixer-tube-api.herokuapp.com/main_categories')
        .then(resp => resp.json())
        .then(data => dispatch({type: LOAD_DATA, payload: data}))
    }
};
```
File updates are done, make sure you push all changes to Github.

Register on [Heroku website](https://signup.heroku.com/login?redirect-url=https%3A%2F%2Fid.heroku.com%2Foauth%2Fauthorize%3Fclient_id%3D0ef71205-3b95-4203-a5e8-c1dbe6b06b43%26response_type%3Dcode%26scope%3Dglobal%252Cplatform%26state%3D0f534d8c-457d-4588-8881-70bda9d13028).

Install Heroku CLI using instructions [here](https://devcenter.heroku.com/articles/heroku-cli) based on your OS.

Now, we will start actual deployment with the backend first. 
1. Navigate into the backend app directory and login into heroku ```heroku login```. 
2. Create Heroku project - ```heroku create fixer-tube-api```
3. Initialize Heroku git repository - ```git remote add heroku git@heroku.com:fixer-tube-api.git```
4. Add files to repository ``` git add .```
5. Commit files ```git commit -m "1st heroku commit"```
6. And push ```git push heroku master```
7. It's time to migrate database ```heroku run rake db:migrate```
8. If you have some data you want to see in seeds.rd ```heroku run rake db:seed```

Our backend is done, and we can check it here - https://fixer-tube-api.herokuapp.com/main_categories.

Now, let's deploy the frontend.
1. Navigate to our frontend folder
2. Create Heroku app set to create-react-app-buildack ```heroku create fixer-tube --buildpack https://github.com/mars/create-react-app-buildpack.git```
3. Initialize Heroku remote ```git remote add heroku git@heroku.com:fixer-tube.```
4. Add files to repository ``` git add .```
5. Commit files ```git commit -m "heroku app created"```
6. And push ```git push heroku master```
7. Now let's see if it will work with ```heroku open```

If everything worked correctly you will be redirected to https://fixer-tube.herokuapp.com/.

Now your project is live online and not just on your computer!!!






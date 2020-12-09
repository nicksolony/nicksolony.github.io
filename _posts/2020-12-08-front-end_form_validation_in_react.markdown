---
layout: post
title:      "Front-end form validation in React"
date:       2020-12-09 02:48:14 +0000
permalink:  front-end_form_validation_in_react
---

If you need to build a React app that connects to your Rails API, you should probably start building a back-end first. It would make sense to build the back-end first and have it render your data. You will need a database, controllers, and models.
It's good practice to make sure that the database can only accept valid data, therefore we would need to add validations and in Rails, validations are added to models. In my particular case, I needed to add validations to my Video model. I wanted to make sure that name, URL, category_id, and brand_id fields are present for each video record in my database. Also, I wanted to make sure that there are no duplicate names or URLs in my database, so I needed to validate the uniqueness of the name and URL.

```
validates :name,:url, :category_id,:brand_id, presence: true
validates :name,:url, uniqueness: true
```

Adding code above to my Video model would not allow me to commit any new videos without those specified values present and wouldn't allow any name or URL duplicates.

The next step would be to get my controller up to speed to handle validation.

```
def create
        if video = Video.create(video_params)
            render json: video
        else
            render json: video.errors.full_messages
        end
end
```

Now created controller would render a newly created video if it passes validation otherwise it would render errors with full messages describing those errors. So even though our Rails validation, I've chosen a different approach to handle form validation in my React app.

Instead of handling validation on the server/database level, I decided that I will work with client-side validation instead. The main benefit of client-side validation - it's faster form validation because we don't need to fetch any data to the server and wait for a response.

In order to handle errors, I updated my component state as follows:
```
state = {
        name:'',
        description:'',
        url:'',
        brandId:null,
        categoryId:null,
        errors: {
            name:"* Required",
            url:"* Required",
            brandId:"Please select Brand, if can't find specific brand choose other",
            categoryId:"Please select Category",
        }
    };
```
In my state, I now have error values, that I will work with to validate the form and present errors.

In React we need to make sure that our state is updated when we update form information since those are connected. We would normally create a handleChange function which would be in charge of updating the state each time data in form changes, at the same time we would also be updating our error messages in our state.

```
 handleChange = (e) => {
        const {name, value} = e.target;
        let errors = this.state.errors;

        switch (name) {
            
            case 'name': 
              if (value==='') {
                  errors.name = "Name can't be blank!"
              } else if (this.props.videos.find(video=>video.name===value)) {
                errors.name = "Video with this name exist!"
              } else {
                errors.name = ''
              }
              break;
            case 'url': 
              if (value==='') {
                  errors.url = "URL can't be blank!"
              } else if (this.props.videos.find(video=>video.url===value)) {
                errors.url = "Video with this url exist!"
              } else {
                errors.url = ''
              }
              break;
            default:
              break;
          }
        
          this.setState({errors, [name]: value})  
    };
```

The case state inside handleChange function declares an error message for each particular value or removes an error message.

On form submission we need to verify if data in the form is valid, lets create a form validator. It will check if there are any error messages and return whether a form is valid.

```
validateForm = (errors) => {
        let valid = true;
        Object.values(errors).forEach(value => {
            value.length > 0 && (valid = false)
        });
        return valid;
    }
```
And finally, we just need to add validateForm function inside our handleSubmit and check if the form is valid. We can also send an alert if the form is not valid.


```
handleSubmit = (e) => {
        e.preventDefault();
        if (this.validateForm(this.state.errors)) {
            this.createNewVideo(this.state);
        } else {
            alert("Please fill in required fields")
        }
    };
```
		
It's pretty easy to set up server-side validation in React. It works fast and you don't need to worry about fetch too much.



---
layout: post
title:      "Nested Forms and Resources"
date:       2017-11-16 12:51:01 -0500
permalink:  nested_forms_and_resources
---

Nested forms and resources are so often feared due to their complex looks and requirements, however once down to the knit and grit of it, they are actually not so dreadful and particularly easy if you understand what exactly is going on. I found that it was too difficult to understand after doing research and it was simply because all the articles based around the two topics were so technically advanced in terminology, and to be quite frank, I needed it dumbed down just a tad bit to really understand the mechanics of it. 

For my project, I created an application that allows a user to sign up or login, as well as login via Facebook, to join a community of people who love and appreciate books. Users can add new books along with their reviews or leave reviews on pre-existing books that other users have added. This was all nice and easy to understand, but the big question around the project was, where do I fit in my nested resources and forms. In order for me to have really understood where to interject a nested resource and form, I needed to understand what the necessity was and why, also I needed to understand the differences and the particular roles of each. My initial misconception was that they both played a role together, but then I realized that wasn't the case


Lets first start with nested resources. According to the Ruby on Rails guides, nested resources are a hierarchy of routes that capture a parent and child model role. In the instance of a book review, we have two separate objects: a Book and a Review. The Review belongs to the Book, and the Book has many reviews. In a nested resource route, this would be built out like so: 

resources :books do
    resources :reviews
 end
	

The indentation here shows the relationship between the parent and child. You can then access the routes that are provided to you by this nested resource via rake routes and you will find similarly the CRUD action routes are also built out, and the controller#action still depends on the Review controller, using rails magic of assumption and opinion that although Book is the parent, we are still attemping to build Reviews for the parent object. FYI, You can filter your routes by using the [only:] syntax in case there are any routes that are of no use to you. This provides a cleaner tree of routes and gets rid of anything unnecessary. 
	
book_reviews GET    /books/:book_id/reviews(.:format)          reviews#index
                       POST   /books/:book_id/reviews(.:format)          reviews#create
       new_book_review GET    /books/:book_id/reviews/new(.:format)      reviews#new
      edit_book_review GET    /books/:book_id/reviews/:id/edit(.:format) reviews#edit
           book_review GET    /books/:book_id/reviews/:id(.:format)      reviews#show****
					 
	Now that are routes are defined, a user can go to the book page, add a review and the association and relationship will be highlighted via the nested resource. Showing that the new review is for the book with id of 1. Like so: 
	
	http://localhost:3000/books/1/reviews/new
	
	We then go to our controller and define our actions:
	
	def new
    @review = Review.new
  end
	
  def create
    @book = Book.find(params[:book_id])
    @review = @book.reviews.build(review_params)
    @review.user = current_user
    if @review.save
     flash[:success] = "Thanks for the review!"
     redirect_to book_path(@book)
    else
     render :new
    end
  end
	
	Our action new calls for a new instance of Review and stores it as an instance variable to be later accessed by the view, which will essentially be the New Review form. This is the tricky part, lets rewind. Again, Book is the parent and Review is the child. And this is shown in our route /book/:id/review/new. This needs to be known in our create action in order to submit and associate the form efficiently. First we will capture the book id and store it an instance variable. We will then build a books reviews passing in the review params as an argument and store that in a review variable. Lastly, we associate the current user with the reviews they leave. This is defined by a has_many, through: relationship. A User has many books through reviews, and a Book has many users through reviews. 
	
All this is great, but why do we need all this? The form: 
In our Views/Reviews/new form, we will access the information predefined in our controller and build our form like so:

 <%= form_for [:book, @review]  do |book_review| %>

      <%= render 'shared/error_messages', object: book_review.object %>

          <%= book_review.label :title %>
          <%= book_review.text_field :title, class: 'form-control' %>

          <%= book_review.label :content %>
          <%= book_review.text_area :content, class: 'form-control' %>

          <%= book_review.label :rating %>
          <%= book_review.number_field :rating, class: 'form-control' %>

          <%= book_review.submit "Submit", class: "btn btn-primary" %>

    <% end %>
		
		Here, we see that the form is for a book_review but the fields are based around the review params and not the book. 

	 
Next we will discuss nested forms: 				 	
	
	First things first, and I wish I had been able to just read this in one of the 20 articles I searched for a clue: What I discoverd was that nested forms are created for a quality user experience. In the case of my app, I would not want a user to have to first add a new book, then proceed to a separate page and add a review. We live in an age today where we quickly lose our patience online, and user experience has everything to do with that. So my goal as a developer is to want to create a nice and convenient UX, that allows a user to do two steps in one and continue on with their experience. Introducing nested forms. 
	
	Lets retrace, the initial action here it to create a new Book, but to make UX light and easy, we want to also create a review form. So it is clear to see that the review will be nested inside in the new Book form. We do not have to create any special routes for this relationship, the form will simply be produced by the new_book URI. localhost:3000/books/new. 
	
	Next we look at our Book controller new and create actions. 
	
	def new
    @book = Book.new
    @book.reviews.build(params[:reviews_attributes])
  end

  def create
    @book = Book.new(book_params)
    if @book.save
      redirect_to books_path(@book)
    else
      render :new
    end
  end

First we instantiate a new instance of Book, and store it in our @book instance variable. We then build what is going to be the nested form from the book instance variable by using the magic of association. A book has many reviews. @book.reviews. But wait... where did reviews_attributes come from and what does it mean. When building a nested form, the parent object must be able to accept the properties of the child form. This is done by defining a method in the parent object like so : 

accepts_nested_attributes_for :reviews

This automatically creates a setter and getter that can simply be accessed by reviews_attributes. This is what will be used to build the form:

<%= form_for @book do |book_form| %>

    <%= render 'shared/error_messages', object: book_form.object %>


          <%= book_form.label :title %>
          <%= book_form.text_field :title, class: 'form-control' %>

          <%= book_form.label :author %>
          <%= book_form.text_field :author, class: 'form-control' %>

            <%= book_form.fields_for :reviews do |rev_form| %>

            <%= rev_form.label :title %>
            <%= rev_form.text_field :title %>

            <%= rev_form.label :content %>
            <%= rev_form.text_area :content %>

            <%= rev_form.label :rating %> (1-5)
            <%= rev_form.number_field :rating %>

            <%= rev_form.hidden_field :user_id, value: current_user.id %>

            <% end %>

          <%= book_form.submit %>

    <% end %>


Here we use form_for to build the parent form but then use fields_for to build the nested form. Fields_for works in conjuction with the acceptes_nested_attributes to capture the review params and build the submitted form with the correct fields. 

 




	
	
	

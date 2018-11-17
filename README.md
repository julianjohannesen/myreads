# MyReads README

# What is this?

This applicaiton is inspired by Goodreads and other sites like it. It allows users to place books on virtual bookshelves; move those books from shelf to shelf; and search for and add new books. 

The app uses React and for now, only implements a front end with a limited selection of books and possible search terms. The list of search terms can be found in SEARCH_TERMS.md

This project was created to fulfill a requirement in Udacity's Front-End Web Development Nanodegree program.

# How to Install and Launch the Application

The application can be installed with 'npm install' and launched with 'npm start'.

# Development Notes

## React Component Tree

index.js
|
App.js
|
Search.js ----- ListBooks.js
|               |
Books.js        BookShelf.js
|               |
Book.js         Books.js
|               |
ShelfChanger    Book.js
                |
                ShelfChanger


## The following is a running list of questions of the form "Why am I getting this buggy behavior?" followed by a brief explanation of the fix.

## Why am I getting the "use value on select" warning

So what I was doing before I fixed this was telling the shelf generator that if the shelf id on this iteration is the same as the id of the old shelf for this book, then add the "selected" attribute to this shelf. But that's not the preferred way to handle selected options in React. What you're supposed to do is create a state to represent the selected state of the options and then change that state with setState whenever the onChange event fires. So, I did that and it worked.

This is the check I was using before fixing the problem. I didn't need it after fixing the problem:

```js
if(shelf.shelf === this.props.oldShelf) {
   return (<option key={index} value={shelf.shelf} selected >{shelf.title}</option>);
}
```

## Why isn't anything displaying on the search page. 

The fetch is returning the search for books. And as far as I can tell, everything is working well. I'm not getting any errors. The books just aren't showing up. Instead, I'm getting the one empty book that is the default.

Figured it out.  I was missing a return statement in a function, which is why nothing was showing. 

I also figured out how to show a "Query Term Not Found" message. And how to show a default image if there's no cover image for a book.

## Why am I getting a mime type error on my service worker. 



## At what point do you actually fetch the books?

At the time that Search is called. 

## How do you get each shelf of books? 

Add a state object to ListBooks with entries for current, wantToRead, and read. Each of those entries is an array of book objects, or maybe book IDs. 

Those arrays are then passed first from ListBooks to BookShelf (as this.state.current, etc.), then from BookShelf to Books as "showingBooks" (as this.props.bookList), and then from Books to Book as "book" (as just book).

I've done all of that and it works. 

## How will the books move from shelf to shelf?

This is going to involve a callback function and setState(). But where does the call to the callback function go? It goes in the select element in ShelfChanger, e.g.

```jsx
// a bunch of code
(< select onChange={callback} >
{/* more code */}
)
```
But the callback has to be defined in the same place that the state is set. Or at least, that's my understanding. 

This took a long time to solve. At first I attempted to import the callback function into ShelfChanger, but it turns out that you have to pass it down as a prop from component to subcomponent, starting up in ListBooks and ending in ShelfChanger. 

## How do you use the callback function to add and remove books from the state?

Then it took a while to figure out how to add and remove books from the state. I'm was pretty pleased with myself when I came up with using a self invoking function to push the book object onto the new shelf and then return the shelf array.

At first I had tried to just push the new book, e.g.

```jsx
this.setState(
    {[newShelf] : this.state[newShelf].push(theBook)}
)
```
and I didn't understand why that wasn't working. Of course, what gets returned by that push expression is the index of the new member of the array. But what I needed was the newShelf array with the new book added in. 

## After clicking on Search, using the back button does not return me to the homepage.

I've added the *Link* component (from react-router-dom) in the search component:

```js
<Link to="/" className="close-search">
	Close
</Link>
```

...and list books component:

```js
<div className="open-search">
    <Link to="/search" >Add a book</Link>
</div>
```

... with the *to=""* attribute pointing to either the home page or to search and that got the back button working in the browser. I can now click on the add button and the URL changes. From the search bar, I can now click on the back button and the URL will change back to the homepage URL of "/". 

## At first both the search component and the list books components were showing. I wanted only the list books view to show and to switch over to the search view only when the user clicks on the add book button.

I needed to import Route and Switch into App.js and then add a *Switch* statement with my *Routes* inside that statement and choose search and list books as my two components, one each for each route, using the *path* attribute and then specifying the component with the *component* attribute.

```js
<Switch>
    <Route exact path="/search" component={Search} />
    <Route exact path="/" component={ListBooks} />
</Switch>
```

In the template, the books are just hard coded into the JSX in App.js. I moved that hard coding to a component called ListBooks. 

The shelves and the books that appear on those shelves are all just hard coded. I need to figure out how to use the BooksAPI to fetch all of those books and display them correctly.

To do think that I think I'll need components both for Books and for Shelves. Definitely for Books.



// HTML Part 
<h1>Instructions</h2>
<p>Below is a pared-down demonstration of React and Redux working together. It has the essential elements you need to connect/subscribe a React component to the Redux store for data, to dispatch actions, and to update the store's state using a reducer.</p>
<p><strong>Challenge 1: Fix the existing code</strong></p>
<p>When you enter a name and a course and hit the "Add Instructor" button, it should update the instructors list in the view. However, it isn't working right now, and lucky you, you get to debug it. Note: This is more of a React review challenge than a Redux one. </p>
<ol>
  <li>Why is the <strong>instructorsList</strong> component not rendering? Fix this first so that you can see the list. Hint: It's a small but important typographical error.</li>
  <li>In the AddInstructor component, the event handler has not been bound correctly. Can you fix it? There are two different and acceptable ways to do this. Refer to <a href="https://codepen.io/minae/pen/KJMYEO" target="_blank">this demo</a> -- specifically, GoodExample1 and ArrowExample2.</li>

</ol>

<p><strong>Challenge 2: Update the fixed code</strong></p>
<p>
For this part of the challenge, add a 'Color' field to the form, next to "Course Taught". Then update the code so that the background color of the instructor name renders as the color that you enter. You will need to update multiple places in the code for this to work: in the reducer, the action, and a couple of other places in addition to the form. </p>
<p>Note: In the reducer, instructor.color is currently hardcoded to black (#000). When you are at this part of the challenge, remove this line completely along with the other changes.</p>

<p>Good luck!</p>

<div id="root"></div>

<!-- original pen by minae lee -->
<!-- with thanks to https://codepen.io/allanpope/pen/GqjxPz for a jumpoff point -->



// CSS Part 
body {
  font-family: 'Lucida Sans Typewriter', 'Lucida Console', monaco, 'Bitstream Vera Sans Mono', monospace; 
}

.instructor {
  color: white; 
  padding: 5px;
}




// JavaScripts Part
const { createStore } = Redux;
const { Provider, connect } = ReactRedux;
// You have learned to use 'import' to bring in components from libraries in your React project. Using const is another way to do it that works better in Codepen. Do not be concerned about the difference at this time.

/* Data */
const INSTRUCTORS = [{
	name: 'Michael Steinberg',
  id: 0,
	color: 'blue',
  course: 'React',
}, {
	name: 'James Pritchett',
  id: 1,
	color: 'green',
  course: 'React',  
}, {
	name: 'Mathew Moser',
  id: 2,  
	color: 'orange',
  course: 'React',  
}, {
  name: 'Brent Schneider',
  id: 3,
  color: 'purple',
  course: 'React',  
}];


class App extends React.Component {
  render() {    
    return (
      <div>
        <h1>Nucamp Instructors</h1>
        <hr />
        <AddInstructor addInstructor={this.props.addInstructor} />
        <hr />
       <InstructorsList instructors={this.props.instructors} />
       </div>
     )
  }
}

class InstructorsList extends React.Component {
  render() {
    return (
				<ul>
					{this.props.instructors.map((instructor) =>
						<li key={instructor.id}>
							<Instructor instructor={instructor} />
						</li>
					)}
				</ul>     
    )
  }
}

class AddInstructor extends React.Component {

  constructor(props) {
    super(props);
    
    this.inputName = null;
    this.inputCourse = null;
    this.inputColor = null;
    
    this.setTextInputRef = inputElement => {
      console.log(inputElement.id);
      switch(inputElement.id) {
        case "name": this.inputName = inputElement; break;
        case "course": this.inputCourse = inputElement; break;
        case "color": this.inputColor = inputElement; break;
      }
    };    
  }
  
  handleSubmit(e) {
    e.preventDefault(); 
    this.props.addInstructor(this.inputName.value, this.inputCourse.value,this.inputColor.value);  
    e.target.reset();
  }
  
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label for="name">Name </label>
        <input id="name" type="text" ref={this.setTextInputRef} />
        <label for="course"> Course Taught: </label>
        <input id="course" type="text" ref={this.setTextInputRef} />         
        <label for="color">  Color: </label>
        <input id="color" type="text" ref={this.setTextInputRef} />
        <button type="submit"> Add Instructor</button>
      </form>
     )
  }
}

class Instructor extends React.Component {
  render() {
    let style = {background: this.props.instructor.color};
    return (            
      <p style={style} className="instructor">
        {this.props.instructor.name} - {this.props.instructor.course}
      </p>
    )
  }
}


/* ACTION TYPE */
const ADD_INSTRUCTOR = 'ADD_INSTRUCTOR';


/* ACTION */
const addInstructor = (name, course) => ({
  type: ADD_INSTRUCTOR,
  payload: {
    name: name,
    course: course
  }
});


/* REDUCER */

const Reducer = (state = INSTRUCTORS, action) => {
  switch (action.type) {
    case ADD_INSTRUCTOR:
      let instructor = action.payload;
      instructor.id = state.length;
      return state.concat(instructor);
    default:
      return state;
  }
};


/* STORE & CONNECTIONS TO STORE */

const store = createStore(Reducer, INSTRUCTORS);

const mapStateToProps = state => {
  return {
    instructors: state
  }
};

/* This is a slightly different syntax for mapDispatchToProps than you've seen in your course project - as a function rather than as an object. See https://react-redux.js.org/using-react-redux/connect-mapdispatch#defining-mapdispatchtoprops-as-a-function for an explanation. The syntax is not too different, and you will encounter this style in many projects. */
const mapDispatchToProps = dispatch => ({
  addInstructor: (name, course) => dispatch(addInstructor(name, course))
});


// The below line is written in a specific way to work with Codepen. Outside of Codepen, you would export default connect([args])(App). 
const AppExport = connect(mapStateToProps, mapDispatchToProps)(App);

/* FINAL RENDER - here, we have wrapped our root component with <Provider>  */
ReactDOM.render(<Provider store={store}><AppExport /></Provider>, document.getElementById("root"));

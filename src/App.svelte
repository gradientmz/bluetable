<!-- I'm going to be leaving comments here since other people might read the code

First, here are the imports. Bluetable uses the font Inter from Google fonts,
And also Font Awesome (which provides the icon used in the blue button). -->

<svelte:head>
	<script src="https://kit.fontawesome.com/fcdf22faa4.js" crossorigin="anonymous"></script>
</svelte:head>

<!-- Here are the script tags. In Svelte, this is where pretty much all of the code goes! -->
<script>
	// These are some of the variables/states I'm defining.
	// The next few are for things that need to be displayed, like the current element.
	
	var inputcolor = "";
	
	var currentincorrect = false;
	
	var element = "";
	
	var number = "";
	var symbol = "";
	var weight = "";
	
	var completed = 0;
	var correct = 0;
	var incorrect = 0;
	var accuracy = "...";

	var yes = false;

	var maxvar = 118;
	
	// These next two are vars/states bound to input boxes, meaning any text that gets
	// typed will automatically be updated here.
	
	var numberinput = "";
	var symbolinput = "";

	// This function returns a random number between the two parameters passed.

	function random(min, max){
    return Math.floor(Math.random()*(max - min + 1) + min);
	}
	
	// This is the get function, where the data Bluetable uses gets retreived from
	// an API. This one is a public REST API that returns periodic table data
	// in json format.
	
	 function get() {
		 // This makes get() only return elements between a and 18 when the box is checked
		if (yes == true) {
			maxvar = 18;
		}
    fetch(`https://neelpatel05.pythonanywhere.com/element/atomicnumber?atomicnumber=${random(1, maxvar)}`)
      .then((response) => response.json())
      .then((data) => {
				// Here, the data gets assigned to the variables above
				element = data.name
        number = data.atomicNumber
				symbol = data.symbol
			
				console.log(number, symbol)
      });
  }
	
	// The submit function is what gets called when the blue button is clicked.
	
	function submit() {
		// The input boxes' colors are actually set with vars as well!
		inputcolor = "background-color: white;"
		
		// This part calculates the user's accuracy, but only after they've
		// gotten 3 questions correct.
		if (completed >= 2) {
			if (incorrect == 0 && correct > 0) {
				accuracy = 100
			} else {
				accuracy = Math.round((1 - (incorrect / correct)) * 100)
			}
		}
		
		// This part is the main section of submit. It checks if the user
		// got the answer or not, and acts accordingly
		if (currentincorrect == false) {
			if (numberinput == number && symbolinput == symbol) {
				console.log("All correct!")
				numberinput = ""; symbolinput = "";
				correct++; completed++;
				get()
			} else {
				numberinput = number
				symbolinput = symbol

				currentincorrect = true

				console.log("You got something wrong...")
				inputcolor = "background-color: rgba(110, 16, 18, 0.79)"
				incorrect++;
			}
		} else {
			currentincorrect = false
			get()
			numberinput = ""; symbolinput = "";
		}
	}
	
get()
</script>

<!-- That's the end of the script section! Here is all of the CSS, which dictates
what the site will look like -->

<style>
	@font-face {
		font-family: "Inter";
		src: url("Inter-Regular.ttf") format("ttf");
	}
	body {
		font-family: 'Inter', sans-serif;
		background-color: whitesmoke;
		background-size: cover;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		overflow: hidden;
	}
	.periodic {
		border-radius: 0.5rem;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		padding: 0rem 1.75rem;
	}
	.periodic-text {
		text-align: center;
		font-size: 2rem;
		width: 100%;
		height: 4rem;
		border: none;
		border-radius: 0.5rem;
		transition: 0.5s;
		box-shadow: 0 4px 8px 0 rgba(0, 0, 
0, 0.1), 0 6px 20px 0 rgba(0, 0, 0, 0.1);
	}
	.element {
		font-size: 3rem;
		margin: 0;
		text-align: center;
	}
	.stats {
		margin-top: 0.5rem;
		margin-bottom: 2rem;
		font-weight: normal;
		text-align: center;
	}
	.submit {
		margin-top: 1rem;
		font-size: 1.5rem;
		background-color: #00a6ff;
		color: white;
		width: 100%;
		height: 4rem;
		transition: 0.2s;
		border-radius: 0.5rem;
		border: none;
		box-shadow: 0 4px 8px 0 rgba(0, 0, 
0, 0.1), 0 6px 20px 0 rgba(0, 0, 0, 0.1);
	}
	.nfs {
		width: 50px;
		height: 50px;
	}
	.eighteencheck {
		margin-top: 1rem;
	}

	/* This media query changes the CSS slightly when the screen is small (for mobile devices)! */
	
	@media only screen and (max-width: 450px){
		.element {
			font-size: 2.5rem;
		}
		.stats {
			font-size: 1em;
		}
		.periodic-text {
			font-size: 1.65rem;
		}
	}
</style>

<!-- And lastly, here's the HTML! It's pretty short, but it serves as the "skeleton"
of the website and is important. You can see a lot of the variables at the beginning
of the project used here.-->

<body>
	<h1 class="element">{element}</h1>
	<h3 class="stats">{correct} correct | {incorrect} incorrect | {accuracy}% accuracy</h3>
	<div class="periodic">
		<input style={inputcolor} bind:value={numberinput} class="periodic-text" placeholder="Number"/>
		<input style={inputcolor} bind:value={symbolinput} class="periodic-text" placeholder="Symbol"/>
		
		<button on:click={submit} class="submit"><i class="fas fa-chevron-square-right fa-lg"></i></button>
	</div>

	<label class="eighteencheck" style="text-align: center;">
		<input type=checkbox bind:checked={yes}>
		Only study elements 1-18
	</label>

	<p style="text-align: center;">
		Bluetable by Mark Zhou at NFS
	</p>
	<a href="https://github.com/gradientmz/bluetable/blob/master/src/App.svelte">
	<img class="nfs" alt="NFS school logo" src="https://i.ibb.co/P96qRkz/2019-logo.png">
	</a>

</body>

<!-- Thank you for reading! -->
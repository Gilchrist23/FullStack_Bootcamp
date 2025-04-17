<script>
	import { onMount } from 'svelte';
	onMount(() => {
		// variable declaration
		const inputs = document.querySelectorAll('.input');
		// const inputLeft= document.querySelector('input--left');
		// const inputRight= document.querySelector('input--right');
		const buttons = document.querySelectorAll('.btn');
		const buttonLeft = document.getElementById('btn--left');
		const buttonRight = document.getElementById('btn--right');
		const dropdowns = document.querySelectorAll('.dropdown-content');
		const dropdownItems = document.querySelectorAll('.dropdown-item');
		const dropdownLeft = document.getElementById('dropdown--left');
		const dropdownRight = document.getElementById('dropdown--right');
		const items = document.querySelectorAll('.dropdown-item');
		const container = document.querySelector('.container');
		console.log(container);
		// initializations
		let isActive = -1;
		let currentActive = '';
		let selectedUnit = '';
		let previousActive = '';

		const unitTypes = {
			Foot: 'Length',
			Inch: 'Length',
			Cm: 'Length',
			Kilogram: 'Mass',
			'Pounds (lbs)': 'Mass',
			Celsius: 'Temperature',
			Fahrenheit: 'Temperature',
			Kelvin: 'Temperature'
		};

		const compatibleUnits = {
			Length: ['Foot', 'Inch', 'Cm'],
			Mass: ['Kilogram', 'Pounds (lbs)'],
			Temperature: ['Celsius', 'Fahrenheit', 'Kelvin']
		};

		const toggleHiddenClass = function (active = -1) {
			console.log('active component in toggle class: ' + active);
			dropdowns[active].classList.toggle('hidden');
		};

		const convert = function (value = 0, from = '', to = '') {
			if (from === 'Celsius' && to === 'Fahrenheit') {
				return (value * 9) / 5 + 32;
			} else if (from === 'Fahrenheit' && to === 'Celsius') {
				return ((value - 32) * 5) / 9;
			} else if (from === 'Celsius' && to === 'Kelvin') {
				return value + 273.15;
			} else if (from === 'Foot' && to === 'Inch') {
				return value * 12;
			} else if (from === 'Inch' && to === 'Foot') {
				return value / 12;
			} else if (from === 'Cm' && to === 'Inch') {
				return value / 2.54;
			} else if (from === 'Inch' && to === 'Cm') {
				return value * 2.54;
			} else if (from === 'Kilogram' && to === 'Pounds (lbs)') {
				return value * 2.20462;
			} else if (from === 'Pounds (lbs)' && to === 'Kilogram') {
				return value * 0.453592;
			}
			return '';
		};

		// function handeling button click event
		buttons.forEach((button, i) => {
			button.addEventListener('click', function () {
				if (isActive !== -1) {
					if (isActive === 0) {
						isActive = i;
						toggleHiddenClass(isActive);
					} else {
						isActive = i;
						toggleHiddenClass(isActive);
					}
				} else {
					isActive = i;
					dropdowns[i].classList.toggle('hidden');
				}
			});
		});

		items.forEach(function (item) {
			item.addEventListener('click', function () {
				const parentDropdown = item.closest('.dropdown-content');
				isActive = parentDropdown.id.includes('left') ? 0 : 1;
				currentActive = isActive === 0 ? 'left' : 'right';
				previousActive = currentActive;
				const activeBtn = document.getElementById(`btn--${currentActive}`);
				activeBtn.textContent = item.textContent;
				parentDropdown?.classList.add('hidden');
				isActive = -1;

				selectedUnit = document.getElementById(`btn--${currentActive}`)?.textContent;
				const unitCategory = unitTypes[selectedUnit];
				const options = compatibleUnits[unitCategory];
				const currentActiveEl = document.getElementById(`dropdown--${previousActive}`);
				const targetActive = previousActive === 'left' ? 'right' : 'left';
				const targetActiveEl = document.getElementById(`dropdown--${targetActive}`);

				for (let items of targetActiveEl?.children) {
					const unit = items.textContent;
					if (!options.includes(unit)) {
						items.classList.add('disabled-item');
					} else if (unit === document.getElementById(`btn--${previousActive}`)?.textContent) {
						items.classList.add('disabled-item');
					} else {
						items.classList.remove('disabled-item');
					}
				}

				for (let items of currentActiveEl?.children) {
					items.classList.remove('disabled-item');
				}
			});
		});

		inputs.forEach(function (input, i) {
			input.addEventListener('keydown', function (e) {
				let number;
				if (e.key === 'Enter') {
					let isValid = false;
					try {
						number = Number(input.value);
						if (isNaN(number)) {
							throw new TypeError('input is not a valid number');
						} else {
							isValid = true;
						}

						// convert(number,,);
					} catch (err) {
						alert('Invalid type' + err.message);
					}

					if (isValid) {
						isActive = i;
						currentActive = i === 0 ? 'left' : 'right';

						selectedUnit = document.getElementById(`btn--${currentActive}`)?.textContent;
						const unitCategory = unitTypes[selectedUnit];
						const options = compatibleUnits[unitCategory];

						const targetActive = isActive === 0 ? 'right' : 'left';
						console.log('target:' + targetActive);

						const targetUnit = document.getElementById(`btn--${targetActive}`)?.textContent;
						if (options.includes(targetUnit)) {
							const output = convert(number, selectedUnit, targetUnit);
							document.getElementById(`input--${targetActive}`).value = output;
						}
					}
				}
			});
		});
	});
</script>

<head>
	<title>Unit Converter</title>
</head>
<body>
	<div class="container">
		<input id="input--left" class="input" type="text" />
		<span class="equals">=</span>
		<input id="input--right" class="input" type="text" />
		<div class="dropdown-btn left">
			<button class="btn" id="btn--left">Foot</button>
			<ul id="dropdown--left" class="dropdown-content hidden">
				<li class="dropdown-item disabled-item">Foot</li>
				<li class="dropdown-item">Inch</li>
				<li class="dropdown-item">Cm</li>
				<li class="dropdown-item">Kilogram</li>
				<li class="dropdown-item">Pounds (lbs)</li>
				<li class="dropdown-item">Celsius</li>
			</ul>
		</div>
		<div class="dropdown-btn right">
			<button class="btn" id="btn--right">Inch</button>
			<ul id="dropdown--right" class="dropdown-content hidden">
				<li class="dropdown-item">Foot</li>
				<li class="dropdown-item disabled-item">Inch</li>
				<li class="dropdown-item">Kilogram</li>
				<li class="dropdown-item">Pounds (lbs)</li>
				<li class="dropdown-item">Fahrenheit</li>
				<li class="dropdown-item">Kelvin</li>
			</ul>
		</div>

		<!-- <button class="btn">Convert</button> -->
	</div>
</body>

<style>
	/*--- 01 TYPOGRAPHY SYSTEM

- Font sizes (px):
10 / 12 / 14 / 16 / 18 / 20 / 24 / 30 / 36 / 44 / 52 / 62 / 74 / 86 / 98

- Font weights:
Default: 400;
input:500;
btn:600;

- Line height:
Default: 1

- Letter spacing:
0.5px
            
--- 02 COLORS 
- PRIMARY: #f0f2f5
- secondary: #4f46e5 
- Tints:#f0f2f5
- Shades:#cf711f  #5c940d
- Accents:#f59f00
- Grey: 
#333


--- 05 SHADOWS
- Button shadow: 0 5px 15px rgba(79, 70, 229, 0.3)
- Button hover shadow: 0 8px 20px rgba(67, 56, 202, 0.4)
- Container shadow: 0 10px 40px rgba(0, 0, 0, 0.1)

--- 06 BORDER-RADIUS
-input: 8px
-btn: 12px
-container:16px
 
--- 07 WHITESPACE
- Spacing system (px)
2 / 4 / 8 / 12 / 16 / 24 / 32 / 48 / 64 / 80 / 96 / 128


*/
	* {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}

	body {
		font-family: 'Roboto', sans-serif;
		line-height: 1;
		font-weight: 400;
		background-color: #f0f2f5;
		color: #333;
	}
	.container {
		max-width: 700px;
		height: 100vh;
		margin: 0 auto;
		padding: 128px 16px;
		background-color: #fff;
		border-radius: 16px;
		box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
		display: grid;
		grid-template-columns: 250px 1fr 250px;
		align-items: center;
		justify-items: center;

		/* background-color: #212529; */
	}

	.btn {
		display: inline-block;
		font-size: 14px;
		background-color: #4f46e5;
		color: #fff;
		border: none;
		/* height: 60px; */
		width: 100px;
		height: 50px;
		border-radius: 12px;
		text-align: center;
		/* border-top-radius: 12px; */
		font-weight: 600;
		transform: scale(1.05);
		letter-spacing: 0.5px;
		transition: all 0.3s ease;
		box-shadow: 0 5px 15px rgba(79, 70, 229, 0.3);
	}
	.btn:hover {
		background-color: #4338ca;
		box-shadow: 0 8px 20px rgba(67, 56, 202, 0.4);
		transform: scale(1.1);
	}

	.right {
		grid-column: 3;
		grid-row: 2;
	}

	/* INPUT FIELD STYLING */
	.input {
		/* 44 / 52 / 62 */
		display: inline-block;
		font-size: 44px;
		font-weight: 500;
		width: 200px;
		height: 100px;
		border: none;
		padding: 48px;
		border-radius: 8px;
		background-color: #f59f00;
	}

	.equals {
		color: #fff;
		font-size: 44px;
	}
	.hidden {
		display: none;
	}

	/* DROPDOWN STYLING */

	.dropdown-btn {
		position: relative;
	}

	.dropdown-content {
		position: absolute;
		margin: 0;
		width: 100px;
		/* height: 60px; */
		/* background-color:#555; */
		background-color: #74b816;
		overflow: hidden;
	}

	.dropdown-item {
		cursor: pointer;
		font-size: 14px;
		color: #fff;
		list-style: none;
		padding: 4px 8px;
		text-align: center;
		border: 1px solid #ddd;
	}
	.dropdown-item:hover,
	.dropdown-item:active {
		background-color: #cf711f;
	}
	.dropdown-item.disabled-item {
		pointer-events: none;
		cursor: not-allowed;
		opacity: 0.5;
		background-color: #555;
	}
</style>

# Choice Wheel

The choice wheel is a spinning circle made up of equal sized wedges that can be used to make selections randomly.

## Main Points
- The number of entries on the wheel can be altered via an add/remove button and the wheel will adjust the size of each wedge accordingly.
- A random position on the wheel will be calculated when the user clicks on the wheel and the winning entry will be displayed via a modal.
- The wedge dimensions and angles are calculated with javascript and injected into the styles by css-variables.
- Similarly, the calculated rotation is introduced into the css by finding the keyframes and appending new rules into it through javascript.
- The javascript framework I picked was Vue.js while bootstrap and jquery were used to display the modal.
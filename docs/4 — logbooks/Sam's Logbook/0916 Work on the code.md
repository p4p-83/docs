Putting the CV code into the `vision` repo so that James can eventually tee into it from `controller`.

I've written one loop in C to take care of compositing and masking at the same time as efficiently as possible. The idea is that all image arrays are preallocated from the Julia side, and then a Julia-language wrapper will do the job of actually calling the loop.

```c
typedef struct frame {
	uint8_t y[WIDTH][HEIGHT];
	uint8_t u[WIDTH/2][HEIGHT/2];
	uint8_t v[WIDTH/2][HEIGHT/2];
} frame;

typedef uint8_t (mask)[WIDTH][HEIGHT];

void acceleratedCompositingMaskingLoop(
	frame fboard,
	frame fcomp,
	frame fout,
	mask mboard,
	mask mcomp
) {
	
	for (int x = 0; x < WIDTH; ++x) for (int y = 0; y < HEIGHT; ++y) {

		const int lumax = x, lumay = y, chromax = x/2, chromay = y/2;
		#define mean(pixel1, pixel2) ((typeof(pixel1))(((int)(pixel1)+(int)(pixel2))/2))
		
		#define thisY(frame) frame.y[lumax][lumay]
		#define thisU(frame) frame.u[chromax][chromay]
		#define thisV(frame) frame.v[chromax][chromay]

		// basic compositing
		thisY(fout) = mean(thisY(fcomp), thisY(fboard));
		thisU(fout) = mean(thisU(fcomp), thisU(fboard));
		thisV(fout) = mean(thisV(fcomp), thisV(fboard));

		#define isInRange(value, lowerBound, upperBound) (((value) >= (lowerBound)) && ((value) <= (upperBound)))

		// basic masking
		mboard[x][y] = isInRange(thisY(fboard), MASK_BOARD_CUT_IN, MASK_BOARD_CUT_OUT);
		mcomp[x][y] = isInRange(thisY(fcomp), MASK_COMP_CUT_IN, MASK_COMP_CUT_OUT);

		#undef mean
		#undef thisY
		#undef thisU
		#undef thisV
		#undef inRange

	}

}
```

See the `vision` git repo for the latest version of the above.
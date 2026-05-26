---
layout: page
order:
title: Other Programming Languages
heading: Other Programming Languages
description: Using other programming languages that support the BBC micro:bit
permalink: /software/other-languages/
ref: software
lang: en
---

## Overview

Aside from the officially supported editors: [MakeCode](https://makecode.microbit.org)  and [Python](https://python.microbit.org) there are a number of different languages that include support for the micro:bit.

This resource aims to compile a list of these programming languages with a link to the documentation, plus an example program.

## Submissions

To add a new language to the page, [edit the page on  Github](http://github.com/microbit-foundation/dev-docs/edit/master/software/other-languages.md). For a language to be accepted its implementation must be complete enough to display a heart on the display!

Please format the addition using this template:

## Language Name

[Project Homepage](https://example.com)

[Example](https://example.com)

### micro:bit heart

```
    example code to show a heart on the display
```

## Alternate Languages

## Ada

[Project Homepage](https://blog.adacore.com/ada-on-the-microbit)

[Example](https://github.com/AdaCore/Ada_Drivers_Library/tree/master/examples/MicroBit/text_scrolling)

### micro:bit heart

```ada
with MicroBit.Display;

procedure Main is
begin

   loop
      MicroBit.Display.Display ("<3");
   end loop;
end Main;
```
## BASIC (ARMbasic)

[Project Homepage](https://www.coridium.us/coridium/blog/basic-for-microbit-v2)
[Example](https://www.coridium.us/coridium/files/MicroBitV2_display.bas)

### micro:bit heart

```BASIC
' Beating heart on the micro:bit v2 (nRF52833) 5x5 LED matrix.

#define ROW1   21
#define ROW2   22
#define ROW3   15
#define ROW4   24
#define ROW5   19
#define COL1   28
#define COL2   11
#define COL3   31
#define COL4   (32+5)      ' P1.05
#define COL5   30

const ROWS as byte = { ROW1, ROW2, ROW3, ROW4, ROW5 }
const COLS as byte = { COL1, COL2, COL3, COL4, COL5 }

' Two frames packed into one array; one byte per row, bit x set => column x
' lit (bit 0 = leftmost).  base 0 = big heart, base 5 = small heart:
'
'   BIG               SMALL
'   . X . X .  = 10   . . . . .  =  0
'   X X X X X  = 31   . X . X .  = 10
'   X X X X X  = 31   . X X X .  = 14
'   . X X X .  = 14   . . X . .  =  4
'   . . X . .  =  4   . . . . .  =  0
const FRAMES as byte = { 10, 31, 31, 14, 4,   0, 10, 14, 4, 0 }
const BITS   as byte = {  1,  2,  4,  8, 16 }    ' column bit masks for AND test

#define BIG     0
#define SMALL   5


' Multiplex the 5-row frame at FRAMES(base..base+4) for 'passes' refreshes.
SUB show(base, passes)
	dim p, x, y
	for p = 1 to passes
		for y = 0 to 4
			' Set this row's columns: LOW = lit, HIGH = off
			for x = 0 to 4
				if (FRAMES(base + y) AND BITS(x)) <> 0 then
					out(COLS(x)) = 0
				else
					out(COLS(x)) = 1
				endif
			next x

			out(ROWS(y)) = 1      ' enable row -> its lit pixels turn on
			wait(2)               ' hold ~2 ms
			out(ROWS(y)) = 0      ' disable row before the next (no ghosting)
		next y
	next p
END SUB


MAIN:

print "micro:bit v2 heartbeat"

dim i

' All row and column pins are outputs
for i = 0 to 4
	output(ROWS(i))
	output(COLS(i))
next i

' Idle state: rows low (off), cols high (off)
for i = 0 to 4
	out(ROWS(i)) = 0
	out(COLS(i)) = 1
next i

' Beat: big heart (rest), then small heart (contraction)
while 1
	show(BIG,   45)
	show(SMALL, 12)
loop
```

## Rust

[Project Homepage: _Discover Microcontrollers Using Rust_](https://docs.rust-embedded.org/discovery/microbit/)

Examples:
  * [LED Roulette](https://docs.rust-embedded.org/discovery/microbit/05-led-roulette/)
  * [UART: Send a Byte](https://docs.rust-embedded.org/discovery/microbit/07-uart/send-a-single-byte.html)
  * [LED Compass](https://docs.rust-embedded.org/discovery/microbit/09-led-compass/)

### micro:bit heart

```rust
#![deny(unsafe_code)]
#![no_main]
#![no_std]

use cortex_m_rt::entry;
use rtt_target::rtt_init_print;
use panic_rtt_target as _;
use microbit::{
    board::Board,
    display::blocking::Display,
    hal::{prelude::*, Timer},
};

#[entry]
fn main() -> ! {
    rtt_init_print!();

    let board = Board::take().unwrap();
    let mut timer = Timer::new(board.TIMER0);
    let mut display = Display::new(board.display_pins);
    let heart = [
        [0, 1, 0, 1, 0],
        [1, 1, 1, 1, 1],
        [1, 1, 1, 1, 1],
        [0, 1, 1, 1, 0],
        [0, 0, 1, 0, 0],
    ];

    loop {
        // Show heart for 1000ms
        display.show(&mut timer, heart, 1000);
        display.clear();
        timer.delay_ms(1000_u32);
    }
}

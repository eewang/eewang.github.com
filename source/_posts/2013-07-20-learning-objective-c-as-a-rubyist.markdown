---
layout: post
title: "Learning Objective-C As a Rubyist"
date: 2013-07-20 09:31
comments: true
categories:
published: false
---
I recently started exploring iOS development.

1) What's with the '@' sign everywhere?

Contrary to my Ruby sense, the use of '@' signs in Objective-C do not indicate instance variables (instead, those are prefaced with an '_' underscore), but rather illustrate something else entirely.

2) Interface vs. implementation

3) Defining variable types

4) Method calls vs. attribute accessors
- Use of dot notation for specified properties and bracket notation to call methods of an object. However, array.count and [array count] will yield the same thing even though count is technically a method and not a property since it has to calculate the size of the array. Properties shouldn't be things that require calculations.

5) Getters / Setters

6) Strong vs. Weak variables

7) Public vs. Private (interface vs. implementation)

* Implement the card game in Objective-C, Ruby, Javascript and Backbone

What's the role of @synthesize, which is used when I custom define my setter and getter?

When do I have to specify a pointer to a stated data type, and when can I just use the '@' sign to create a variable'?

What is general best practice for organizing code? Do all class methods get grouped together and same with instance methods?

{% codeblock lang:objc %}

#import "PlayingCard.h"

@implementation PlayingCard

- (int)match:(NSArray *)otherCards
{
    int score = 0;
    if ([otherCards count] == 1) {
        PlayingCard *otherCard = [otherCards lastObject];
        if ([otherCard.suit isEqualToString:self.suit]) {
            score = 1;
        }   else if (otherCard.rank == self.rank) {
            score = 4;
        }
    }
    return score;
}

- (NSString *)contents
{
    NSArray *rankStrings = [PlayingCard rankStrings];
    return [rankStrings[self.rank] stringByAppendingString:self.suit];
}

@synthesize suit = _suit;

+ (NSArray *)validSuits
{
    return @[@"♠", @"♣", @"♥", @"♦"];
}

- (void)setSuit:(NSString *)suit
{
    if ([[PlayingCard validSuits] containsObject:suit]) {
        _suit = suit;
    }
}

- (NSString *)suit
{
    return _suit ? _suit : @"?";
}

+ (NSArray *)rankStrings
{
    return @[@"?", @"A", @"2", @"3", @"4", @"5", @"6", @"7", @"8", @"9", @"10", @"J", @"Q", @"K"];
}

+ (NSUInteger)maxRank
{
    return [self rankStrings].count - 1;
}

- (void)setRank:(NSUInteger)rank
{
    if (rank <= [PlayingCard maxRank]) {
        _rank = rank;
    }
}

@end

{% endcodeblock %}
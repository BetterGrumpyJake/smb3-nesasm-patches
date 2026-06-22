# Re-Enter Levels Patch
This patch allows for the customization of which tiles are enterable (in in this case allowing completed levels to be re-entered).

## Compatibility
This patch requires some of the unused memory at the end of prg010 from 0x15dd0-0x15de3. This memory might be used by other patches so this patch might be incompatible with your ROM. Verify that these addresses are 0xFF before applying this patch.  And as always, make a backup before applying a patch and completely test after applying a patch.

## Customization
Let's say you want to add/remove entries to the enterable table, but you don't want to modify the source, here is how you would do it.

1. Modify the table to have the tile values you want in it starting at address 0x15dd0 and in the starting example, going until 0x15de3.
2. Update the size of the table at 0x14ed4 to be the new size of your table minus 1.  For example, the new table in this patch has 20 entries so 0x13 is at 0x14ed4 (20 = 0x14 minus 1 = 0x13).

## Code Changes
If the patch is incompatible with your ROM or you just want integrate this feature with your code base directly, here are the source modifications required:

Make the following modification in prg010:
```
LDY #$13        ; Fix the tables size of the special tiles - this needs to match your table size
PRG010_CEC9:
    CMP Map_NewEnterSpecialTiles,Y
```
Note the two changes above:
1. the LDY value to the correct size of the table
2. the name of the special tiles table has "New" added to it

And then at the bottom of prg010, add the following:
```
Map_NewEnterSpecialTiles:
    .byte TILE_TOADHOUSE, TILE_SPADEBONUS, TILE_PIPE, TILE_ALTTOADHOUSE
    .byte TILE_CASTLEBOTTOM, TILE_SPIRAL, TILE_ALTSPIRAL, TILE_PATHANDNUB
    .byte TILE_DANCINGFLOWER, TILE_HANDTRAP, TILE_BOWSERCASTLELL
    .byte TILE_MARIOCOMP_G, TILE_LUIGICOMP_G
    .byte TILE_MARIOCOMP_P, TILE_LUIGICOMP_P
    .byte TILE_MARIOCOMP_R, TILE_LUIGICOMP_R
    .byte TILE_MARIOCOMP_O, TILE_LUIGICOMP_O
    .byte TILE_FORTRUBBLE
```
This table will determine what levels are enterable. The original list is here plus all of the Mario/Luigi variants plus the fort rubble. 

That's it. You can now re-enter levels after they are completed.

*NOTE* - This will also make space tiles and mushroom houses enterable.  The tile set is broken up into 4 quadrants (white, yellow/orange, green/blue, and red). Each quadrant has its own "M" and "L" square to show a level complete. Any tile in a quadrant will use the "M" or "L" for that quadrant when complete. For example, a standard level is in the white quadrant so it will have a white "M" when complete. The quicksand or pyramid is in the yellow/orange quadrant, so it will have a yellow "M" when complete, and a red fort will have a red "M" when complete. You can make each of these color "M"/"L" tiles enterable individually. The example above shows making them all enterable.  Let say you wanted to use yellow mushroom houses and have them not re-enterable. That would mean the quicksand and pyramid would also not be re-enterable (since they also get the yellow/orange "M"/"L"). The yellow fort, however, should still be re-enterable since it gets the fort rubble rather than the "M"/"L"
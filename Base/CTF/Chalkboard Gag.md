#forensic #writeup 

## Matt Groening sent me an unused chalkboard gag, he says there's something special inside of it.

Wrote a script to recognise the changed line and got the flag.

```
# Define the phrase you're searching for
phrase = "I WILL NOT BE SNEAKY"

# Initialize an empty string to store differing letters
all_differing_letters = ""

# Open the text file
with open("chalkboardgag.txt", "r") as file:
    # Iterate through each line in the file
    for line in file:
        # Remove leading/trailing whitespaces and newline characters
        clean_line = line.strip()
        
        # Check if the line contains the phrase
        if phrase not in clean_line:
            # Compare each character in the line with the corresponding character in the phrase
            for i in range(min(len(phrase), len(clean_line))):
                if clean_line[i] != phrase[i]:
                    # Append differing letters to the accumulator string
                    all_differing_letters += clean_line[i]
        else:
            # If the line contains the phrase, move to the next line
            continue

# Print all differing letters found in all lines
print("All differing letters:", all_differing_letters)
```

Flag: `bcactf{BaRT_W0U1D_B3_PR0uD}`
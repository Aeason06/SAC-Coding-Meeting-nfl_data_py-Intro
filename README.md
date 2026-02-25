# Charlotte SAC Coding Meeting nfl_data_py Intro
Unfortunately, installing this package sucks. It works as intended on Python versions 3.10-12, but  we are going to try to get around installing older Python versions by using this line to install nfl_data_py. I have included CSV files in the repo if we can't get it working. <br>

```Python
!pip install numpy>=2.0 pandas fastparquet appdirs && pip install nfl_data_py --no-deps
```
You should already have this, but run this line just to make sure.
```Python
!pip install Pillow
```
Code for making plot with comments to explain. 
```Python
# Initialize a dictionary to store downloaded logos so we don't fetch the same image twice
logo_cache = {}

def get_logo_from_url(url, zoom=0.08, size=(350, 350), opacity=0.7):
    # Create a unique identifier for the cache based on the URL and desired opacity
    cache_key = (url, opacity)
    
    # Check if we already have this specific logo processed in our dictionary
    if cache_key not in logo_cache:
        # Open the image URL, read the data, and convert it to RGBA (Red, Green, Blue, Alpha)
        with urlopen(url) as response:
            img = Image.open(io.BytesIO(response.read())).convert("RGBA")
            # Resize the logo to a consistent base size using a high-quality filter
            img = img.resize(size, Image.LANCZOS)

            # Isolate the transparency (alpha) channel to modify it
            alpha = img.split()[3]  
            # Scale the existing transparency by the opacity parameter
            alpha = alpha.point(lambda p: int(p * opacity))  
            # Put the modified transparency back into the image
            img.putalpha(alpha)  

            # Store the processed image in the cache
            logo_cache[cache_key] = img
            
    # Return a Matplotlib 'OffsetImage' object which can be placed on a plot
    return OffsetImage(logo_cache[cache_key], zoom=zoom)

# Initialize the Matplotlib figure and axis with a specific width and height
fig, ax = plt.subplots(figsize=(10, 6))

# Assign the X and Y coordinates from the dataframe (Passing and Rushing EPA)
X = merged['p_epa']
y = merged['r_epa']

# Get the list of URLs for the team logos
urls = merged['team_logo_espn']

# Create an invisible scatter plot to automatically scale the axes correctly
ax.scatter(X, y, alpha=0)

# Loop through the data points and URLs simultaneously
for x, y_val, url in zip(X, y, urls):
    if isinstance(url, str):
        # Create an AnnotationBbox to place the image at the specific (x, y) coordinate
        ab = AnnotationBbox(
            get_logo_from_url(url),
            (x, y_val),
            frameon=False # Remove the box border around the logo
        )
        # Add the logo image to the actual plot
        ax.add_artist(ab)

# Calculate the average values for both axes to create a quadrant view
x_mean = X.mean()
y_mean = y.mean()

# Draw a vertical dashed line representing the average Pass EPA
ax.axvline(x_mean, color='black', linestyle='--', alpha=0.5, label=f'Mean X: {x_mean:.2f}')

# Draw a horizontal dashed line representing the average Rush EPA
ax.axhline(y_mean, color='black', linestyle='--', alpha=0.5, label=f'Mean Y: {y_mean:.2f}')

# Set the specific boundaries for the X and Y axes
ax.set_xlim(-0.35, 0.35)
ax.set_ylim(-0.3, 0.175)

# Label the axes and provide a title for the chart
ax.set_xlabel('Pass EPA per Play')
ax.set_ylabel('Rush EPA per Play')
ax.set_title('AVG Rush EPA vs AVG Pass EPA (2025)')

# Display the final visualization
plt.show()
```

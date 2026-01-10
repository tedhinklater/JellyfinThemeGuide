# Jellyfin Theme Guide
A guide/cookbook for making Jellyfin themes

If you want to make your own jellyfin theme, this is for you.

# Where to put it

### Server-wide application

If you want every user on the server to have the CSS applied by default, go to your Dashboard > Branding > CSS, and paste into the custom CSS box.

### Current user application

If you only want it applied to the current user, click your profile picture, go to Display > enable "_Disable server-provided custom CSS code_" and paste your CSS into the box.

# How the CSS works

CSS **only affects jellyfin-web** so that includes any client using the web wrapper, e.g. Chrome, Firefox, Brave, Jellyfin Media Player, Jellyfin app for Android etc.

# Knowing What To Target

In your browser, Press ```F12``` to open Dev Tools, and use the **Inspect Element** button to find what you're looking for

![target](https://github.com/user-attachments/assets/d6f7ee15-8fc8-46c5-a028-a25589060185)

| Element Type     | HTML                                                  | CSS                                     |
|------------------|-------------------------------------------------------|-----------------------------------------|
| Class            | `<div class="card">`                                  | `.card`                                 |
| ID               | `<div id="indexPage">`                                | `#indexPage`                            |
| Multiple Classes | `<div class="card focused">`                          | `.card.focused`                         |
| Descendant       | `<div class="card"><span class="title"></span></div>` | `.card .title`                          |
| Attribute        | `<div data-type="movie">`                             | `[data-type="movie"]`                   |

So to only target Movie cards in the More Like This Section, while they're being focused

```css
#similarCollapsible .card.focused[data-type="Movie"] {
  font-size: 200%;
}
```

Many sub-elements are nested unless specifically Inspected, so look deep if you want to create a specifically-targeted rule

# Rules

## Priority 

HTML defines the elements, CSS (Cascading Style Sheet) positions them. ```Cascading``` is the key word here; when two rules for the same specific element are written, the last/latest CSS rule is the one that gets priority. 
We'll use a card (the poster/picture of a media item such as a movie/episode) as an example:

### 200% will get priority here
```css
.card { font-size: 100%; }

.card { font-size: 200%; }
```

### 100% will get priority here with ```!important``` 
```css
.card { font-size: 100% !important; }

.card { font-size: 200%; }
```

### 100% will be applied to all card elements, and 200% will be applied to the more-specific backdropCard (landscape cards) element inside it
If it applies to that element, the more specific rule will take priority
```css
.card { font-size: 100%; }

.card.backdropCard { font-size: 200%; }
```

# Targeting Devices

### Mobile, Desktop and TV
This will apply ```font-size: 200%``` to all backdropCards on mobile, desktop and TV.
```css
.layout-mobile .card.backdropCard,
.layout-desktop .card.backdropCard,
.layout-tv .card.backdropCard {
  font-size: 200%;
}
```

# Different elements, one rule
You can apply the same rule to different elements.
In this example, cardText is only targeted on desktops, only portraitCards are targeted on mobile, and all cards are targeted on TV. 
```css
.layout-mobile .card.portraitCard,
.layout-tv .card,
.layout-desktop .card.squareCard,
.layout-desktop .card.backdropcard,
.layout-desktop .cardText {
  font-size: 200%;
}
```

# One element, different rules

### Rules based on screen size
1st rule is for horizontal windows/screens <999px

2nd rule is for vertical windows/screens >1000px 
```css
@media (max-width: 999px) and (orientation: landscape) {
  .layout-desktop .cardText {
    font-size: 200%;
    color: #000;
  }
}

@media (min-width: 1000px) and (orientation: portrait) {
  .layout-desktop .cardText {
    font-size: 100%;
    color: #000;
  }
}
```

## Rules based on page elements
In modern clients you can use the ```:has``` and ```:not``` selectors to differentiate between pages
If you want green text on Movie pages, and red text on Show pages, it can be done (if you really wanted to)

We'll target specific pages using cardText as a basic example

### Page with a portraitCard (poster) as the main image
```css
 .layout-desktop .detailImageContainer:has(.portraitCard) .cardText {
  font-size: 200%;
}
```


### Actor Bio Page
```css
.layout-desktop .itemDetailPage:has(.detailImageContainer > .card .cardImageIcon.person) .cardText {
  font-size: 200%;
}
```

### Season (Episode list) Page
```css
.layout-desktop .itemDetailPage:has(.listItem.listItem-largeImage[data-type="Episode"]) .cardText {
  font-size: 200%;
}
```

### Episode Page
```css
.layout-desktop .itemDetailPage:has(.button-link.itemAction.emby-button[data-type="Season"]) .cardText {
  font-size: 200%;
}
```

### Artist Page

```css
.layout-desktop .itemDetailPage:has(.verticalSection[data-type="MusicAlbum"]) .cardText {
  font-size: 200%;
}
```

### Album Page
```css
.layout-desktop .itemDetailPage:has(.nameContainer .parentName.musicParentName) .cardText {
  font-size: 200%;
}
```

### Cast
On desktop layouts, if ```itemDetailPage``` contains a visible guest cast, set .cardText to normal size.

If guest cast is not present, set cardText to 200%

```css
.layout-desktop #itemDetailPage:has(#guestCastCollapsible:not(.hide)) .cardText {
  font-size: 100%;
}

.layout-desktop #itemDetailPage:has(#guestCastCollapsible.hide) .cardText {
  font-size: 200%;
}
```

### Shuffle Button
```css
.layout-desktop .itemDetailPage:has(.button-flat.btnShuffle.detailButton.emby-button) .cardText {
  font-size: 200%;
}
```

### Next Up
```css
.layout-desktop .itemDetailPage:has(.nextUpSection .card.overflowBackdropCard.card-hoverable) .cardText {
  font-size: 200%;
}
```

### Playlists
```css
.layout-desktop .itemDetailPage:has(.button-flat.btnPlaystate.detailButton.emby-button[data-type="Playlist"]) .cardText {
  font-size: 200%;
}
```

### Collection
```css
.layout-desktop #itemDetailPage:has([data-type="BoxSet"]) .cardText {
  font-size: 200%;
}
```

# Hover (Desktop)

This will apply ```font-size: 200%``` on hover, with an instant (0s) transition with a duration of 0.5s

There is also a small transition delay of 0.1s when un-hovering (prevents buggy animation)

```css
.layout-desktop .cardText {
  font-size: 100%;
  transition: font-size 0.5s ease-out 0.1s;
}

.layout-desktop .cardText:hover {
  font-size: 200%;
  transition: font-size 0.5s ease-out 0s;
}
```

# Focus (TV)

This will apply ```font-size: 200%``` on focus, with an instant (0s) transition with a duration of 0.5s

There is also a small transition delay of 0.1s when un-hovering (prevents buggy animation)

```css
.layout-tv .cardText {
  font-size: 100%;
  transition: font-size 0.5s ease-out 0.1s;
}

.layout-tv .cardText:focus {
  font-size: 200%;
  transition: font-size 0.5s ease-out 0s;
}
```

# Adding psuedo-elements

You can up to 2 pseudo-elements (```before``` and ```after```), per html element.

A pseudo-element is attached to a html element, and can be used to add shapes, colour, backgrounds etc.

The 1st adds a black background to cardText

The 2nd adds a black backround to cards but it is anchored to the top-left of the screen
```css
.layout-desktop .cardText::before {
  content: '';
  position: absolute;
  background: #000;
  z-index: -1;
}

.layout-desktop .card::before {
  content: '';
  position: fixed;
  inset: 0;
  background: #000;
  z-index: 0;
  pointer-events: none;
}
```


Fully mapped to atomic design systems, Compounded should be configurable at all levels. Principles follow AD - and compounds are the functionalities (extra layer on top of AD)

Proposition: it should guide you into going from quark to molecule, to compound eventually.

## quarks
- sizing scale
- typography
- colors
- spacing system
- box shadows
- z-layers
- transition timing functions
- borders
- breakpoints

## atoms
- Label
- Badge
- Button
- Text
- Input
- TextArea
- Toggle
- Checkbox
- RadioGroup
- Slider (form slider)
- List / ListItem
- Icon
- TableCell / TableRow / TableHeading
- Arrow
- Divider (hr)
- Paper
- Sound

## compounds (API only, in the form of hooks)

- Slider (image lists horizontally, vertically,...)
- Table
- DropDown
- AutoComplete
- Form
- Modal
- Grid
- Snackbar / Toast
- DatePicker
- TimePicker
- Tabs
- Interactable (used by buttons etc - interaction abstractions such as hover, click, select,...)
- Tooltip
- Expandable (accordions)
- Media (audio/video player control api)
- Progress
- Image (image loader/display API)
- File (file API)
- Position (useful for position: sticky implementations etc)
- Draggable
- Filter
- Breadcrumbs
- Pagination
- Browser/Device (api abstractions such as react-fns, but in hooks -- geoposition, scrollposition, windowsize, device motion, ... )
- HREF (implementation of e.g. href=mailto or href=sms - but with protection against scraping somehow, if possible)
- Translation/Locale



## molecules
I don't think we need to be concerned with molecules. In userland you would put together all the atoms together with compound api's to generate the molecules

## organisms
In same light as molecules, probably we shouldn't care about this

## API design:

Theming:

```js
// create quarks with factories
const baseSize = 16;
const steps = [0.25, 0.5, 0.75, 1, 1.5, 2, 3, 4, 5, 8, 12, 16, 24, 32, 40, 48];

const scale = makeSizingScale({steps, baseSize);
/** outputs responsive sizing scales - base of all paddings, margins, fontsizes,...
 * example:
  {
    sm: {
      0: 3
      1: 6
      2: 9
      3: 12
      4: 18
      5: 24
      6: 36
      7: 48
      8: 60
      9: 96
      10: 144
      11: 192
      12: 288
      13: 384
      14: 480
      15: 576
    }
    md: {
      0: 5
      1: 10
      2: 15
      3: 20
      4: 30
      5: 40
      6: 60
      7: 80
      8: 100
      9: 160
      10: 240
      11: 320
      12: 480
      13: 640
      14: 800
      15: 960
    }
    fixed: {
      // fixed steps based on baseSize
    }
  }
 */

const fontWeights = {
  light: 300,
  medium: 500,
  bold: 700,
  extraBold: 800
}

const typography = makeTypography(fontFamily, fontWeights);
/**
 * outputs different elements associated with font family, with line-height based on sane defaults
 * typography.p {fontFamily, fontWeight: fontWeights.medium}
 * typography.h1
 * typography.h2
 * ...
 *
 * of course, always possible to define your own typography object granularly. This is just a helper with UX best practices.
 */

const quarks = {
  scale,
  typography
}

// atoms receive theme from context
const app = () => (
  <Theme theme={{...quarks}}>
    {SomeComponent}
  </Theme>
)

const MyStyledComponent = styled.div`
  padding: ${props => props.scale[2]}
`

// using the theme for creating your own component
const SomeComponent = () => {
  const {scale, typography, colors, fontWeights} = useTheme();

 return (
   <Fragment>
    {/* pre made  Text atom*/}
     <Text
        padding={scale[2]} // responsive padding based on screen size
        margin={scale.fixed[2]} // fixed margin
        variant={typography.p} // sets font family and element
        color={colors.primary} // sets color
        weight={fontWeights.light}// sets fontWeight
      />
      {/* or use the theme in a styled component */}
      <MyStyledComponent scale={scale} />
    </Fragment>
 )
}
```

using the interactionAPI
```js
const SomeInteractiveComponent = () => {
  const { onHover, hovered, onDown, isDown } = useInteractionAPI();

  return (
    <Button
      onClick={console.log}
      onHover={onHover}
      variant={hovered ? "primary" : "secondary"}
      inset={isDown}
      onMouseDown={onDown}
    />
  )
}
```


// extensive api example for a dropdown component
```js
const DropDownInput = () => {
  // useDropdown api is basically useContext, with dropdown context provided from the api
  const {inputValue, setDropped, componentRefs, selectedItem} = useDropdownAPI();

  return (
      <Fragment>
        <Input // this would be an atom from compounded or from your own components
          type="text"
          value={inputValue}
          onFocus={() => setDropped(true)} // control when you open the item drawer yourself!
          onBlur={e => {
            // using refs is also pretty easy, we can just get them from the API
            // for example, we don't want to close the dropdown when clicking on one of the dropdown items
            // this is logic we could/should probably move to a helper function.
            // can't move it to core, because it adresses the browser event api!
            if (e.relatedTarget && Object.keys(componentRefs).length >= 0) {
              for (const ref in componentRefs) {
                if (e.relatedTarget.contains(componentRefs[ref])) {
                  return;
                }
              }
            }
            // clicked outside the dropdown, set dropped to false
            setDropped(false);
          }}
          onChange={interactWithValue(api.handleInputChange)}
        ></input>
        selected: {selectedItem.label}
      </Fragment>
  )
}


const DropDownItems = () => {
  const {dropped, inputValue, setComponentRef, filteredItems, onSelectItem} = useDropdownAPI();
  if (dropped && inputValue.length >= 2 ) {
    return (
        <div ref={c => setComponentRef(c, `dropdownItemContainer`)}>
          {filteredItems
            .map((item, i) => (
              <div
                onClick={() => onSelectItem(item)}
                ref={c => setComponentRef(c, `dropDownItem_${item.value}`)}
                key={item.value}
                tabIndex={i}
              >
                {item.label}
              </div>
            )
          )}
        </div>
    )
  }
}

const Dropdown = props => (
  <DropdownAPI {...props}>
    <DropDownInput />
    <hr />
    <DropDownItems />
  </DropdownCore>
);

// usage in, for example a class (can do the same with hooks)
// note that it _is_ possible to create multiple dropdowns. Each will use their own context
<Dropdown
  selectedItem={this.state.selectedItem}
  onSelectItem={id => this.setState({ selectedItem: id })}
  items={[
    { label: "hello", value: "hello" },
    { label: "world", value: "world" },
    { label: "new_world", value: "new_world" },
  ]}
  theme
/>
```

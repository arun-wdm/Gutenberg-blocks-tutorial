# Gutenberg Block presentation
References
* https://developer.wordpress.org/block-editor/getting-started/create-block/
* https://developer.wordpress.org/block-editor/how-to-guides/block-tutorial/block-controls-toolbar-and-sidebar/

#### Prerequisite
* NPM: 8.19.x 
* Node 16.20
* npx

---

### Initial Plugin Setup 

1. Run the following command from plugins folder:

```
npx @wordpress/create-block gutenpride --template @wordpress/create-block-tutorial-template
```
The above code downloads and installs a base plugin gutenpride for us to start

---

2. cd into gutenpride folder and run
```
npm start
```

---

### Exploring the  base plugin

The main plugin file gutenberg.php contains just a function to register a block using register_block_type()

The block metadata defenition is inside `build/block.json`

You can find the same inside `src/block.json` - This is where we should be editing to make changes.

To know more about block attributes refer: https://developer.wordpress.org/block-editor/reference-guides/block-api/block-attributes/


### Developing on the block

1. First Let us add color to the string by adding a color pallet setting to the block

Head over to `block.json` and add the following attribute.
```
"text_color": { 
    "type": "string",
    "default": "#ffffff" 
}
```

Next inside `edit.js` let us add add the colour palet and function to handle color change

Following code adds color pallet to the block settings - should be added inside the div block
```
<InspectorControls key="setting">
    <div id="gutenpride-controls">
        <fieldset>
            <legend className="blocks-base-control__label">
                { __( 'Text color', 'gutenpride' ) }
            </legend>
            <ColorPalette // Element Tag for Gutenberg standard colour selector
                onChange={ onChangeTextColor } // onChange event callback
            />
        </fieldset>
    </div>
</InspectorControls>
```

We will need to import the following for the above code to work
```
import { useBlockProps,ColorPalette,InspectorControls } from '@wordpress/block-editor';
import { __ } from '@wordpress/i18n';

```

This code saves the changed color attribute -  Should be added before the return statement 
```
	const onChangeTextColor = ( hexColor ) => {
		setAttributes( { text_color: hexColor } );
	};
```

You can see that the color palet appeared on the right.

Now inorder for the style to take effect inside the block we need to add style inside TextController
```
style={ {
    color: attributes.text_color,
} }
```

Finally Edit function will look like this:
```
export default function Edit( { attributes, setAttributes } ) {
	const blockProps = useBlockProps();
	const onChangeTextColor = ( hexColor ) => {
		setAttributes( { text_color: hexColor } );
	};
	return (
		<div { ...blockProps }>
			<InspectorControls key="setting">
				<div id="gutenpride-controls">
					<fieldset>
						<legend className="blocks-base-control__label">
							{ __( 'Text color', 'gutenpride' ) }
						</legend>
						<ColorPalette // Element Tag for Gutenberg standard colour selector
							onChange={ onChangeTextColor } // onChange event callback
						/>
					</fieldset>
				</div>
			</InspectorControls>
			<TextControl
				style={ {
					color: attributes.text_color,
				} }
				value={ attributes.message }
				onChange={ ( val ) => setAttributes( { message: val } ) }
			/>
		</div>
	);
}
```


Next for the style to appear in the frontend we need to add the style to `save.js` file
```
style={ {
    color: attributes.text_color,
} }
```

save function will look like this
```
export default function save( { attributes } ) {
	const blockProps = useBlockProps.save();
	return <div 
		{ ...blockProps }
		style={ {
			color: attributes.text_color,
		} }
		>
		{ attributes.message }
		</div>;
}
```


### Dynamic block

To make this block dynamic we need add a callback function while registering the block, so modify the register_block_type like this:
```
function create_block_gutenpride_block_init() {
	register_block_type( __DIR__ . '/build', array(
		'render_callback' => 'wdm_render_callback',
	) );
}
add_action( 'init', 'create_block_gutenpride_block_init' );

function wdm_render_callback($block_attributes, $content){
	return "Helllo world";
}
```

So the above function gives the complete power to control the frontend side of the block uing PHP.
Refer: https://developer.wordpress.org/block-editor/how-to-guides/block-tutorial/creating-dynamic-blocks/

# DirectX-TextureFromMemory
couldn't find hardly any info about this online so i'll drop the solution i wrote here.

credits: azurilex

## Usage
```c
	/* @note: readable version of array
	 *
	 *   	 RED(0,0), GREEN(1,0), BLUE(2,0)
	 *	 GREEN(0,1), BLUE(1,1), RED(2,1)
	 *	 BLUE(0,2), RED(1,2), GREEN(2,2)
	 */
	 
	 // @ note: byte array representing pixel data with a width and height of 3

	std::uint8_t map[] = {
		0xff, 0x00, 0x00, 0xff, 0x00, 0xff, 0x00, 0xff, 0x00, 0x00, 0xff, 0xff, // @note: red, green, blue
		0x00, 0xff, 0x00, 0xff, 0x00, 0x00, 0xff, 0xff, 0xff, 0x00, 0x00, 0xff, // @note: green, blue, red
		0x00, 0x00, 0xff, 0xff, 0xff, 0x00, 0x00, 0xff, 0x00, 0xff, 0x00, 0xff  // @note: blue, red, green
	};
	
	
  	ID3D11ShaderResourceView* p_logo_or_something;
        // @note: d3d11 device, pointer to d3d11 shader resource view (where you'll store your texture), array, width, height
        if (!load_image(p_device, &p_logo_or_something, map, 3, 3))
            throw std::runtime_error("failed to load texture from memory");
```

```c
bool drawing::load_image(ID3D11Device* p_device, ID3D11ShaderResourceView** shader_view, const std::uint8_t* data, const UINT width, const UINT height)
{
	// @note: width * (sizeof byte * 4 for r,g,b,a)
	// @note: another way of calculating bytes per row of pixels would be sizeof(data) / height
	const D3D11_SUBRESOURCE_DATA init_data = {
		data,
		static_cast<DWORD>(width * (sizeof(std::uint8_t) * 4)),
		0
	};

	const D3D11_TEXTURE2D_DESC desc = {
		width,
		height,
		1,
		1,
		DXGI_FORMAT_R8G8B8A8_UNORM,
		DXGI_SAMPLE_DESC{1, 0},
		D3D11_USAGE_DEFAULT,
		D3D11_BIND_SHADER_RESOURCE,
		0
	};
	
	ID3D11Texture2D* tex = nullptr;
	if (SUCCEEDED(p_device->CreateTexture2D(&desc, &init_data, &tex)))
	{
		D3D11_SHADER_RESOURCE_VIEW_DESC srv_desc = {
			DXGI_FORMAT_R8G8B8A8_UNORM,
			D3D11_SRV_DIMENSION_TEXTURE2D
		};
		srv_desc.Texture2D.MipLevels = 1;

		if (FAILED(p_device->CreateShaderResourceView(tex, &srv_desc, shader_view)))
			return false;

		return true;
	}

	return false;
}
```

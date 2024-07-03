# OpenGL vs Vulkan
## Vulkan is new and has a lot of bugs, on the other hand OpenGL is time-tested

Vulkan was released in 2017 and OpenGL in 1992, so what do you expect from 7 year old software? By the way OpenGL has a lot less functions than Vulkan. You won't be able to create hardware ray tracer or even rendering without a single triangle on the OpenGL.

## Vulkan is hard

It isn't. Can you tell what does this OpenGL code do within a second?

```cpp
glGenBuffers(1, &ssbo);
glBindBuffer(GL_SHADER_STORAGE_BUFFER, ssbo);
glBufferData(GL_SHADER_STORAGE_BUFFER, sizeof(data), data​, GLenum usage);
glBindBufferBase(GL_SHADER_STORAGE_BUFFER, 3, ssbo);
glBindBuffer(GL_SHADER_STORAGE_BUFFER, 0);
```

Well, it creates storage buffer. This code creates storage buffer aswell.

```cpp
VkBufferCreateInfo bufferCreateInfo{};
bufferCreateInfo.sType = VK_STRUCTURE_TYPE_BUFFER_CREATE_INFO;
bufferCreateInfo.size = sizeof(data);
bufferCreateInfo.usage = VK_BUFFER_USAGE_STORAGE_BUFFER_BIT | VK_BUFFER_USAGE_SHADER_DEVICE_ADDRESS_BIT;
bufferCreateInfo.sharingMode = VK_SHARING_MODE_EXCLUSIVE;
vkCreateBuffer(device->device, &bufferCreateInfo, nullptr, &buffer);

VkMemoryAllocateFlagsInfo allocFlagsInfo{};
allocFlagsInfo.sType = VK_STRUCTURE_TYPE_MEMORY_ALLOCATE_FLAGS_INFO;
allocFlagsInfo.flags = VK_MEMORY_ALLOCATE_DEVICE_ADDRESS_BIT;
VkMemoryAllocateInfo allocInfo{};
allocInfo.sType = VK_STRUCTURE_TYPE_MEMORY_ALLOCATE_INFO;
allocInfo.allocationSize = memRequirements.size;
allocInfo.memoryTypeIndex = FindMemoryType(memRequirements.memoryTypeBits, VK_MEMORY_PROPERTY_HOST_VISIBLE_BIT | VK_MEMORY_PROPERTY_HOST_COHERENT_BIT);
allocInfo.pNext = &allocFlagsInfo;
vkAllocateMemory(device->device, &allocInfo, nullptr, &memory);

vkMapMemory(device->device, memory, 0, size, 0, &data);
vkBindBufferMemory(device->device, buffer, memory, 0);
```

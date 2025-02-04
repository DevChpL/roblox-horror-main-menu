# roblox-horror-game-scripts
## Pairing System(incomplete)
### Module Script


```
local Group = {}
Group.__index = Group

--[[
 Creating Group with properties :
 Group_Owner: Player
 Group_Number: IntValue
 Group_Members: Table with properties Ready and Player Object 
 Group_Name: String

--]]

function Group.new(Owner:Player?,GroupName:string)
	local index
	if #game.ServerStorage.Groups:GetChildren() == 0 then
		index = 1
	else
		for i ,v in ipairs(game.ServerStorage.Groups:GetChildren()) do
			index = i + 1
		end
	end
	return setmetatable({
		Group_Owner = Owner;
		Group_index = index;
		Group_Members = {{Player = Owner,Ready = false}};
		Group_Name = GroupName;
		IsFull = false;
	},Group)
	
end

--[[
Update:Method()
1- it check the serverstorage for an int value named the same as group to check if the group already exist
2- it checks if there are Group Members in the object if yes it create the ui and display it on the member screen
]]
--[[


]]

function Group:Update()
	if not game.ServerStorage.Groups:FindFirstChild(self.Group_Name) then
		local newIndex = Instance.new("IntValue",game.ServerStorage.Groups)
		newIndex.Name = self.Group_Name
		newIndex.Value = self.Group_index
		for i ,v in ipairs(game:GetDescendants()) do
			if v.Name == "JoinUi" then
				print("got em")
				if v:FindFirstChild("PlayerCount"..tostring(self.Group_index)) then
					print("thing found")
					local Button = v:FindFirstChild("PlayerCount"..tostring(self.Group_index))[self.Group_index]
					Button.Text = self.Group_Name
					print("done")
					print(Button.Text)
				end
			end
		end
		
	end
	if #self.Group_Members ~= 0 then
		for i ,v in self.Group_Members do
			if v.Player.ClassName == "Player" then
				
			end
		end
	end
	
end





return Group

```

## Camera Manager
### Module Script
```


local Camera = {}
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
Camera._index = Camera
local Tween
local i = 0
local Debounce = false
function Camera.ToPos(Camera1:Camera,Camera_Part: Part,CameraFocus:Part)
	Camera1.CameraType = Enum.CameraType.Scriptable
	Camera1.CFrame = CFrame.lookAt(Camera_Part.Position,CameraFocus.Position)
	Camera1.CameraSubject = CameraFocus
	Camera.DefaultMoving(Camera1,Camera_Part,CameraFocus)
	
end
function Camera.DefaultMoving(Camera1:Camera,Camera_Part:Part, CameraFocus:Part)
	local Rendering = RunService.RenderStepped:Connect(function()
		Camera1.CameraSubject = CameraFocus
		if not Debounce then
			if i > 360 then
				i = 0
			end
			Debounce = true
			
			local Radius = (Camera_Part.Position - CameraFocus.Position).Magnitude
			local X = CameraFocus.Position.X + math.cos(math.rad(i)) * Radius
			local Y = CameraFocus.Position.Y + math.sin(math.rad(i)) * Radius
			Tween = TweenService:Create(Camera1,TweenInfo.new(1/60),{CFrame = CFrame.lookAt(Vector3.new(X,Camera1.CFrame.Position.Y,Y),CameraFocus.Position),
			})
			if Tween.PlaybackState ~= Enum.PlaybackState.Playing then
				Tween:Play()
				i +=0.1
				Camera1:SetAttribute("IsPlaying",true)
			end
			Tween.Completed:Connect(function()
				Debounce = false
			end)
			if not Camera1:GetAttribute("IsPlaying") then
				Rendering:Disconnect()
			end

		end
		
	end)
end
function Camera.StopPrologue(Camera1:Camera,Camera_Part:Part, CameraFocus:Part)
	local IsPlaying =  Camera1:GetAttribute("IsPlaying")
	IsPlaying = false
end
return Camera




```

## Buttons_Handler
### Local Script
```
--Roblox Services
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local ContentProvider = game:GetService("ContentProvider")
local CollectionService = game:GetService("CollectionService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
--Modules
local Group_Service = require(game.ReplicatedStorage.Pairings.Pairing_Module)
--Loading Variables 
ContentProvider:PreloadAsync(Players.LocalPlayer.PlayerGui:GetDescendants())
ContentProvider:PreloadAsync(ReplicatedStorage:GetDescendants())
-- Variables
local PlayerGUI = Players.LocalPlayer.PlayerGui
local Rendering: Frame = PlayerGUI.MainMenu.Rendering
local Main_Menu = ReplicatedStorage.UserInterfaces.MainMenu.MainGuis:Clone()
local PlayOutput = ReplicatedStorage.UserInterfaces.PlayOutput.PlayFrame:Clone()
local Solo_Button :ImageButton = PlayOutput.Solo
local Solo_Output = ReplicatedStorage.UserInterfaces.SoloOutput:Clone()
local Duo_Button = PlayOutput.Duos
local Duo_Output = ReplicatedStorage.UserInterfaces.DuoOutput:Clone()
local Cache = Instance.new("Folder")
Cache.Parent = workspace
Cache.Name = "Cache"
local Create_Button = Duo_Output.CreateOrJoin.Create
local Join_Button = Duo_Output.CreateOrJoin.Join
local Create_Output = ReplicatedStorage.UserInterfaces.CreateOutput:Clone()
local Join_Output = ReplicatedStorage.UserInterfaces.JoinOutput:Clone()
local Controls_Button = Main_Menu.Controls
local Control_Output = ReplicatedStorage.UserInterfaces.ControlsOutput:Clone()
local Control_Close_Button = Control_Output.ControlsTab.ControlsTab.CloseButton
local Shop_Button = Main_Menu.Shop
local Shop_Output = ReplicatedStorage.UserInterfaces.ShopOutput:Clone()
local Shop_Close_Button = Shop_Output.ShopGui.CloseButton
local Info = TweenInfo.new(0.1)
local Ready_To_Iterate = false
--Arrays
--functions
function CleanRenderFile()
	for i ,v in ipairs(Rendering:GetChildren()) do
		v.Parent = Cache
	end
end
function hasProperty(object, propertyName)
	local success, _ = pcall(function() 
		object[propertyName] = object[propertyName]
	end)
	return success
end
--Proccesing
Main_Menu.Parent = Rendering
-- Play Button
Main_Menu.Play.Activated:Connect(function()
	local DefaultSize = Main_Menu.Play.Size
	TweenService:Create(Main_Menu.Play,Info,{Size = UDim2.new(Main_Menu.Play.Size.X.Scale * 1.1,0,Main_Menu.Play.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Main_Menu.Play,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	PlayOutput.Parent = Rendering
end)
Solo_Button.Activated:Connect(function()
	local DefaultSize = Solo_Button.Size
	TweenService:Create(Solo_Button,Info,{Size = UDim2.new(Solo_Button.Size.X.Scale * 1.1,0,Solo_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Solo_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Solo_Output.Parent = Rendering
end)
Duo_Button.Activated:Connect(function()
	local DefaultSize = Duo_Button.Size
	TweenService:Create(Duo_Button,Info,{Size = UDim2.new(Duo_Button.Size.X.Scale * 1.1,0,Duo_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Duo_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Duo_Output.Parent = Rendering
end)
Create_Button.Activated:Connect(function()
	local DefaultSize = Create_Button.Size
	TweenService:Create(Create_Button,Info,{Size = UDim2.new(Create_Button.Size.X.Scale * 1.1,0,Create_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Create_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Create_Output.Parent = Rendering

end)
Join_Button.Activated:Connect(function()
	local DefaultSize = Join_Button.Size
	TweenService:Create(Join_Button,Info,{Size = UDim2.new(Join_Button.Size.X.Scale * 1.1,0,Join_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Join_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Join_Output.Parent = Rendering
	game.ReplicatedStorage.Test:FireServer()
end)
--Controls Button
Controls_Button.Activated:Connect(function()
	local DefaultSize = Controls_Button.Size
	TweenService:Create(Controls_Button,Info,{Size = UDim2.new(Controls_Button.Size.X.Scale * 1.1,0,Controls_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Controls_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Control_Output.Parent = Rendering
end)
Control_Close_Button.Activated:Connect(function()
	local DefaultSize = Control_Close_Button.Size
	TweenService:Create(Control_Close_Button,Info,{Size = UDim2.new(Control_Close_Button.Size.X.Scale * 1.1,0,Control_Close_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Control_Close_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Main_Menu.Parent = Rendering
end)
--Shop Button
Shop_Button.Activated:Connect(function()
	local DefaultSize = Shop_Button.Size
	TweenService:Create(Shop_Button,Info,{Size = UDim2.new(Shop_Button.Size.X.Scale * 1.1,0,Shop_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Shop_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Shop_Output.Parent = Rendering
end)
Shop_Close_Button.Activated:Connect(function()
	local DefaultSize = Shop_Close_Button.Size
	TweenService:Create(Shop_Close_Button,Info,{Size = UDim2.new(Shop_Close_Button.Size.X.Scale * 1.1,0,Shop_Close_Button.Size.Y.Scale * 1.1,0)}):Play()
	task.wait(Info.Time)
	TweenService:Create(Shop_Close_Button,Info,{Size = DefaultSize}):Play()
	task.wait(Info.Time)
	CleanRenderFile()
	Main_Menu.Parent = Rendering
end)


```

# roblox-horror-game-scripts
#incomplete pairing system
Module Script: 
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
#UI_Buttons_Handler
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
#Camera Manager
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

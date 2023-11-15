

```lua
local UserInput = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")

local Utility = require(ReplicatedStorage.Modules.NikoModules.Utility)
local PlayerData = require(ReplicatedStorage.Modules.WeeveModules.PlayerData)

local PathTestRemote = ReplicatedStorage.Remotes.PathTest

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character
local Humanoid = Character:FindFirstChild("Humanoid")
local HRP = Character:FindFirstChild("HumanoidRootPart")

local ActiveVisuals = workspace.ActiveVisuals

local MarkerTemp = ReplicatedStorage.Storage.PathVisuals.Marker

local SelectedUnits = {}

local function Move()
	local Goal = Utility.GetMousePosition()

	if Goal then
		local GoalPos = Goal.Position
		
		for i, Dummy in SelectedUnits do
			PathTestRemote:FireServer(Dummy, GoalPos)
		end
		
		local Marker = MarkerTemp:Clone()
		
		Marker.Position = GoalPos + Vector3.new(0, 0, Marker.Size.Z/2)
		Marker.Parent = ActiveVisuals
	end
end


local function DeSelect()
	for i, Unit in SelectedUnits do
		Unit.Highlight:Destroy()
	end
	SelectedUnits = {}
end


local ShiftHeld = false


local function Select(Input, GameService)
	if GameService then
		return
	end
		
	if Input.UserInputType == Enum.UserInputType.MouseButton1 then
		local Target = Utility.GetMousePosition()
		
		if Target.Instance.Parent:FindFirstChild("Humanoid") and game.Players:GetPlayerFromCharacter(Target.Instance.Parent) == nil then -- Would also check if its there unit add later
			if #SelectedUnits ~= 0 and ShiftHeld == false then
				print("Ran")
				DeSelect()
			end
			
			local HighLighter = Instance.new("Highlight")
			local UnitCharacter = Target.Instance.Parent
			
			HighLighter.Parent = UnitCharacter
			table.insert(SelectedUnits, UnitCharacter)
		else 
			print("Ran")
			DeSelect()
			SelectedUnits = {}
		end
	end
	
	if Input.UserInputType == Enum.UserInputType.MouseButton2 and #SelectedUnits ~= 0 then
		Move()
	end
	
	if Input.KeyCode == Enum.KeyCode.LeftShift then
		ShiftHeld = true
	end
end


UserInput.InputBegan:Connect(Select)


local function NotHolding(Input, GameService)
	if Input.KeyCode == Enum.KeyCode.LeftShift then
		ShiftHeld = false
	end
	
end

UserInput.InputEnded:Connect(NotHolding)```

Script is no where near finished it breaks if you give a unit 2 inputs barely worked on this since upload

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Pathfinding = game:GetService("PathfindingService")

local PlayerData = require(ReplicatedStorage.Modules.WeeveModules.PlayerData)

local PathTestRemote = ReplicatedStorage.Remotes.PathTest

local TempNode = ReplicatedStorage.Storage.PathVisuals.PathVisual

local NodeYOffset = .5

local ActiveDummy = {}

local Parameters = {
	AgentRadius = 2;
	AgentHeight = 5;
	AgentCanJump = false;
}


local function PathFind(Player, Dummy, GoalPos, Queue)
	--if not ActiveDummy[Dummy] then
	ActiveDummy[Dummy] = {}
--	end
	
	local PlacedNodes = ActiveDummy[Dummy]
	
	
	
	local Path = Pathfinding:CreatePath(Parameters)
	
	Path:ComputeAsync(Dummy.HumanoidRootPart.Position, GoalPos)
	
	for _, Waypoint in Path:GetWaypoints() do
		local Node = TempNode:Clone()
		
		Node.Position = Waypoint.Position + Vector3.new(0 , NodeYOffset, 0)
		Node.Parent = workspace
		table.insert(PlacedNodes, Node)
	end
	
	for i, Node in PlacedNodes do
		if PlacedNodes[i+1] == nil or i == 1 then
			continue
		end
		
		Node.Beam.Attachment1 = PlacedNodes[i+1].Attachment
	end
	
	for i, Waypoint in Path:GetWaypoints() do
		local Node = PlacedNodes[i]
		Dummy.HumanoidRootPart.Beam.Attachment1 = Node.Attachment
		
		
		Dummy.Humanoid:MoveTo(Waypoint.Position)
		Dummy.Humanoid.MoveToFinished:Wait()
		PlacedNodes[i]:Destroy()
	end
	PlacedNodes = {}
end

PathTestRemote.OnServerEvent:Connect(PathFind)
```

import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import ast

class RobotWorkspacePlot:
    def __init__(self, radius, increased_distance, angle, y_mod, x_mod, zoom_factor):
        self.radius = radius
        self.increased_distance = increased_distance
        self.angle = angle
        self.y_mod = y_mod
        self.x_mod = x_mod
        self.zoom_factor = zoom_factor

        # Calculate the original and adjusted circle centers
        self.calculate_circle_positions()

        # Initialize figure and axis
        self.fig, self.ax = plt.subplots()
        self.setup_plot()

    def calculate_circle_positions(self):
        # Original circle centers with increased distance
        x1, y1 = 0, 0
        x2, y2 = x1 + self.increased_distance, y1
        x3, y3 = x1 + self.increased_distance / 2, y1 + self.increased_distance * np.sin(self.angle)

        # Calculate the centroid of the triangle formed by the three circles
        centroid_x = (x1 + x2 + x3) / 3
        centroid_y = (y1 + y2 + y3) / 3

        # Adjust the positions of the circles so that the centroid is at the origin (0, 0)
        self.adjusted_x1, self.adjusted_y1 = x1 - centroid_x - self.x_mod, y1 - centroid_y * self.y_mod
        self.adjusted_x2, self.adjusted_y2 = x2 - centroid_x - self.x_mod, y2 - centroid_y * self.y_mod
        self.adjusted_x3, self.adjusted_y3 = x3 - centroid_x - self.x_mod, y3 - centroid_y * self.y_mod

    def draw_arrows_and_labels(self):
        # Function to draw an arrow between two points, considering the circle's circumference
        def draw_arrow(ax, start, end, radius):
            vector = np.array(end) - np.array(start)
            unit_vector = vector / np.linalg.norm(vector)
            new_start = np.array(start) + unit_vector * radius
            new_end = np.array(end) - unit_vector * radius
            ax.annotate("",
                        xy=tuple(new_start), xycoords='data',
                        xytext=tuple(new_end), textcoords='data',
                        arrowprops=dict(arrowstyle="<->", lw=2))

        # Draw arrows
        # draw_arrow(self.ax, (self.adjusted_x1, self.adjusted_y1), (self.adjusted_x2, self.adjusted_y2), self.radius)
        # draw_arrow(self.ax, (self.adjusted_x2, self.adjusted_y2), (self.adjusted_x3, self.adjusted_y3), self.radius)
        # draw_arrow(self.ax, (self.adjusted_x3, self.adjusted_y3), (self.adjusted_x1, self.adjusted_y1), self.radius)

        # Add text labels
        label_offset = 2.4
        var_fontsize = 16
        self.ax.text(self.adjusted_x3, self.adjusted_y3 + label_offset, 'BUILD AREA', horizontalalignment='center', fontsize=var_fontsize)
        self.ax.text(self.adjusted_x1 - label_offset, self.adjusted_y1, 'STORAGE AREA', horizontalalignment='right', fontsize=var_fontsize)
        self.ax.text(self.adjusted_x2 + label_offset, self.adjusted_y2, 'CHARGING AREA', horizontalalignment='left', fontsize=var_fontsize)


    def setup_plot(self):
        # Setting the background color to white and removing axes
        self.ax.set_facecolor('white')
        plt.axis('off')

        # Draw filled circles with transparency at adjusted positions
        circle1 = plt.Circle((self.adjusted_x1, self.adjusted_y1), self.radius, color='blue', fill=True, alpha=0.5)
        circle2 = plt.Circle((self.adjusted_x2, self.adjusted_y2), self.radius, color='red', fill=True, alpha=0.5)
        circle3 = plt.Circle((self.adjusted_x3, self.adjusted_y3), self.radius, color='green', fill=True, alpha=0.5)

        self.ax.add_artist(circle1)
        self.ax.add_artist(circle2)
        self.ax.add_artist(circle3)

        # Draw arrows and add text labels
        self.draw_arrows_and_labels()

        # Set limits and aspect
        self.ax.set_xlim(-self.increased_distance*2 / self.zoom_factor, self.increased_distance*2 / self.zoom_factor)
        self.ax.set_ylim(-self.increased_distance / self.zoom_factor, self.increased_distance / self.zoom_factor)
        self.ax.set_aspect('equal', adjustable='box')


    def draw_robot(self, robot_pos, body_width, body_height, wheel_size, orientation, robot, cargo, status):

        robot_color = ['grey', 'purple']
        # Extract robot_x and robot_y
        robot_x = robot_pos['x']
        robot_y = robot_pos['y']
        body_color = robot_color[robot]
        back_wheel_color = 'black'
        if status == 'charging':
            back_wheel_color = 'red'
        elif status == 'low_battery':
            back_wheel_color = 'orange'

        # Adjust dimensions based on orientation
        if orientation in ['left', 'right']:
            body_width, body_height = body_height, body_width

        # Define wheel positions and colors based on orientation
        if orientation == 'up':
            wheel_positions_colors = [
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), back_wheel_color),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), back_wheel_color),
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), 'green'),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), 'green')
            ]
        elif orientation == 'down':
            wheel_positions_colors = [
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), 'green'),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), 'green'),
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), back_wheel_color),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), back_wheel_color)
            ]
        elif orientation == 'left':
            wheel_positions_colors = [
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), 'green'),
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), 'green'),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), back_wheel_color),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), back_wheel_color)
            ]
        else: # 'right' orientation
            wheel_positions_colors = [
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), back_wheel_color),
                ((robot_x - body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), back_wheel_color),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y - body_height / 2 - wheel_size / 2), 'green'),
                ((robot_x + body_width / 2 - wheel_size / 2, robot_y + body_height / 2 - wheel_size / 2), 'green')
            ]

        # Create and add wheels with appropriate colors
        for position, color in wheel_positions_colors:
            wheel = plt.Rectangle(position, wheel_size, wheel_size, color=color, fill=True)
            self.ax.add_patch(wheel)

        # Create a rectangle for the robot's body and add it after the wheels
        robot_body = plt.Rectangle((robot_x - body_width / 2, robot_y - body_height / 2),
                                body_width, body_height, color=body_color, fill=True)
        self.ax.add_patch(robot_body)

        # Add cargo text on top of the robot body
        self.ax.text(robot_x, robot_y, cargo, color='black', fontsize=16, horizontalalignment='center', verticalalignment='center')

    def draw_wall(self, num_horizontal_studs, num_vertical_studs):
        print("Drawing wall with", num_horizontal_studs, "horizontal studs and", num_vertical_studs, "vertical studs")
        stud_length = 4  # Length of the horizontal studs
        stud_width = 0.5  # Width of the vertical studs
        wall_height = 7   # Height of the wall
        wall_x_start = 6  # Adjust as needed
        wall_y_start = 0  # Adjust as needed
        enclosure_factor = 1.1

        # Calculate the enclosure size
        enclosure_width = stud_length * enclosure_factor
        enclosure_height = wall_height * enclosure_factor
        enclosure_x_start = wall_x_start - (enclosure_width - stud_length) / 2
        enclosure_y_start = wall_y_start - (enclosure_height - wall_height) / 2

        # Draw horizontal studs
        if num_horizontal_studs > 0:
            y_positions = np.linspace(wall_y_start, wall_y_start + wall_height, num_horizontal_studs)
            for y in y_positions:
                self.ax.plot([wall_x_start, wall_x_start + stud_length], [y, y], color='brown', linewidth=5)

        # Draw vertical studs
        if num_vertical_studs > 0:
            if num_vertical_studs > 1:
                x_positions = np.linspace(wall_x_start, wall_x_start + stud_length, num_vertical_studs)
            else:
                x_positions = [wall_x_start]  # Only one vertical stud at the start

            for x in x_positions:
                self.ax.plot([x, x], [wall_y_start, wall_y_start + wall_height], color='brown', linewidth=5)


        # Encapsulate everything in a larger transparent green box
        self.ax.add_patch(plt.Rectangle((enclosure_x_start, enclosure_y_start), enclosure_width, enclosure_height, fill=True, color='green', alpha=0.3))


    def key_event_handler(event, plot, length, index, df, robot_positions):

        if event.key == 'right':
            index[0] = (index[0] + 1) % length
        elif event.key == 'left':
            index[0] = (index[0] - 1) % length

        # Retrieve set of positions
        current_pos_ = df.iloc[index[0], 1]
        current_pos_ = current_pos_.strip('[]')
        current_pos_list = current_pos_.split(',')
        prev_pos_ = df.iloc[index[0] - 1, 1]
        prev_pos_ = prev_pos_.strip('[]')
        prev_pos_list = prev_pos_.split(',')
        next_pos_ = df.iloc[index[0] + 1, 1]
        next_pos_ = next_pos_.strip('[]')
        next_pos_list = next_pos_.split(',')

        # Retrieve set of actions
        current_action_ = df.iloc[index[0], 2]
        current_action_ = current_action_.strip('[]')
        current_action_list = current_action_.split(',')
        # prev_action_ = df.iloc[index[0] - 1, 2]
        # prev_action_ = prev_action_.strip('[]')
        # prev_action_list = prev_action_.split(',')
        # next_action_ = df.iloc[index[0] + 1, 2]
        # next_action_ = next_action_.strip('[]')
        # next_action_list = next_action_.split(',')

        # Retrieve current cargo
        current_cargo_ = df.iloc[index[0], 3]
        current_cargo_ = current_cargo_.strip('{}')
        current_cargo_list = current_cargo_.split(' ')

        # Retrieve studs left
        studs_left_ = df.iloc[index[0], 4]
        studs_left = ast.literal_eval(studs_left_)



        print(current_pos_list)
        print(current_action_list)

        robot_pos_list = []
        robot_orientation_list = []
        wall_built_studs = [0, 0]

        for index, pos in enumerate(current_pos_list):        
            if pos == 'C':
                robot_pos = robot_positions['c_pos']
                orientation = 'up'
            elif pos == 'B':
                robot_pos = robot_positions['b_pos']
                orientation = 'up'
            elif pos == 'S':
                robot_pos = robot_positions['s_pos']
                orientation = 'up'
            elif pos == 'T':
                if prev_pos_list[index] == 'C' and next_pos_list[index] == 'B':
                    robot_pos = robot_positions['bc_pos']
                    orientation = 'up'
                elif prev_pos_list[index] == 'C' and next_pos_list[index] == 'S':
                    robot_pos = robot_positions['cs_pos']
                    orientation = 'left'
                elif prev_pos_list[index] == 'B' and next_pos_list[index] == 'C':
                    robot_pos = robot_positions['bc_pos']
                    orientation = 'down'
                elif prev_pos_list[index] == 'B' and next_pos_list[index] == 'S':
                    robot_pos = robot_positions['sb_pos']
                    orientation = 'down'
                elif prev_pos_list[index] == 'S' and next_pos_list[index] == 'C':
                    robot_pos = robot_positions['cs_pos']
                    orientation = 'right'
                elif prev_pos_list[index] == 'S' and next_pos_list[index] == 'B':
                    robot_pos = robot_positions['sb_pos']
                    orientation = 'up'

            robot_pos_list.append(robot_pos)
            robot_orientation_list.append(orientation)    
        
        # for index, action in enumerate(current_action_list):
        #     if action == 'CHRG':

        wall_built_studs[0] = 2 - studs_left[0]
        wall_built_studs[1] = 4 - studs_left[1]

        plot.update_plot(robot_pos_list, robot_orientation_list, current_cargo_list, wall_built_studs[0], wall_built_studs[1], body_width=1, body_height=1.5, wheel_size=0.3)

    
    def update_plot(self, robot_pos_list, robot_orientation_list, current_cargo_list, h_studs, v_studs, body_width, body_height, wheel_size):
        self.ax.clear()
        self.setup_plot()

        offset_amount = 1
        for index, robot_pos in enumerate(robot_pos_list):
            offset_x = robot_pos['x'] + (2*index -1) * offset_amount
            updated_robot_pos = {"x": offset_x, "y": robot_pos['y']}
            orientation = robot_orientation_list[index]
            cargo = current_cargo_list[index]
            self.draw_robot(updated_robot_pos, body_width, body_height, wheel_size, orientation, index, cargo, status='charging',)

        self.draw_wall(h_studs, v_studs)

        plt.pause(0.1)
        plt.draw()
        plt.get_current_fig_manager().window.state('zoomed')


def main():
    # Initialize the RobotWorkspacePlot class with specified parameters
    plot = RobotWorkspacePlot(radius=2, increased_distance=12, angle=np.pi / 3, y_mod=1.5, x_mod = 5, zoom_factor=1.5)
    df = pd.read_csv('data\gt_case3.csv')

    robot_positions = {
        "s_pos": {"x": plot.adjusted_x1, "y": plot.adjusted_y1},
        "b_pos": {"x": plot.adjusted_x3, "y": plot.adjusted_y3},
        "c_pos": {"x": plot.adjusted_x2, "y": plot.adjusted_y2},
        "sb_pos": {"x": (plot.adjusted_x1 + plot.adjusted_x3) / 2, "y": (plot.adjusted_y1 + plot.adjusted_y3) / 2},
        "bc_pos": {"x": (plot.adjusted_x3 + plot.adjusted_x2) / 2, "y": (plot.adjusted_y3 + plot.adjusted_y2) / 2},
        "cs_pos": {"x": (plot.adjusted_x2 + plot.adjusted_x1) / 2, "y": (plot.adjusted_y2 + plot.adjusted_y1) / 2}
    }

    # wall_built_studs = [0, 0]

    index = [-1]
    length = df.shape[0]

    # plt.ion()
    plot.fig.canvas.mpl_connect('key_press_event', lambda event: RobotWorkspacePlot.key_event_handler(event, plot, length, index, df, robot_positions))
    
    plt.show()
        
        
    # Draw a robot at the center of the plot
    # plot.draw_robot(robot_positions['sb_pos'], body_width=1, body_height=1.5, wheel_size=0.3, orientation='up', robot='r1')

    # Display the plot
    

# Call the main function when the script is executed
if __name__ == "__main__":
    main()
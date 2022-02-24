class Contributor:

    def __init__(self, name, num_skills):
        self.name = name
        self.num_skills = num_skills
        self.free = True
        self.skills = dict()

    def add_skill(self, skill_name, level):
        self.skills[skill_name] = level


class Project:

    def __init__(self, name, n_days, score, best_before, num_roles):
        self.name = name
        self.n_days = n_days
        self.score = score
        self.best_before = best_before
        self.num_roles = num_roles
        self.roles = dict()
        self.order_roles = []

    def add_roles(self, role_name, level_thresh):
        self.roles[role_name] = level_thresh
        self.order_roles.append(role_name)


class Assignment:

    def __init__(self, name, contrib_list):
        self.name = name
        self.contrib_list = contrib_list


def input_fn():
    c, p = map(int, input().split())
    contributors, projects = [], []

    for _ in range(c):
        name, n_skills = input().split()
        contributor = Contributor(name, int(n_skills))
        for _ in range(int(n_skills)):
            skill, level = input().split()
            contributor.add_skill(skill, int(level))
        contributors.append(contributor)

    for _ in range(p):
        name, n_days, score, best_before, num_roles = input().split()
        project = Project(name, int(n_days), int(score), int(best_before), int(num_roles))
        for _ in range(int(num_roles)):
            skill, level = input().split()
            project.add_roles(skill, int(level))
            project.order_roles.append(skill)
        projects.append(project)

    return contributors, projects


def assignment_fn(contributors, projects):
    assignments = []
    sorted_projects = sorted(projects, key = lambda x: x.n_days)
    for pr in sorted_projects:
        assign_dict = dict()
        for cont in contributors:
            if cont.free:
                for skill in cont.skills.keys():
                    if (skill in pr.roles.keys()) and (skill not in assign_dict.keys()):
                        if cont.skills[skill] >= pr.roles[skill]:
                            assign_dict[skill] = cont.name
                            cont.free = False
        if len(assign_dict.keys()) == len(pr.roles.keys()):
            contrib_list = []
            for roles in pr.order_roles:
                contrib_list.append(assign_dict[roles])
            ass = Assignment(pr.name, contrib_list)
            assignments.append(ass)
    print(len(assignments))
    for ass in assignments:
        print(ass.name)
        for index, value in enumerate(ass.contrib_list):
            if index % 2==0:
                print(value, end = " ")
        print("")


if __name__ == "__main__":
    projects, contributors = input_fn()
    assignment_fn(projects, contributors)
